

```
from datetime import time, datetime

from mercuryclient.types.id_verification.request import PassportDetails
from thefuzz import fuzz

from e_organisation.models import EsthenosOrgApplication, logger
from esthenos import get_mercury_client
from esthenos.settings import MERCURY_ID_VALIDATION_PROFILE, NBFC_PRODUCTS, PRIMARY_PRODUCTS, \
    ALLOWED_IDS
from celery.utils.log import get_task_logger
from typing import Tuple, List

ENABLE_ID_VERIFICATION = True
id_name_map = {
    "Pancard": "PAN_CARD",
    "Passport": "PASSPORT",
    "Voter ID": "VOTER_ID",
    "Driving Licence": "DRIVING_LICENSE",
}


def check_id_validation_task(app_id: str):
    app = EsthenosOrgApplication.objects.get(application_id=app_id)
    if (
            app.kyc_validity_status in ["", "PENDING"]
            and ENABLE_ID_VERIFICATION
            and app.product.code in NBFC_PRODUCTS + PRIMARY_PRODUCTS
    ):
        id_status, id_messages = id_validation_check(app)
        if id_messages:
            app.rule_status.extend(id_messages)
            app.failure_reasons.extend(id_messages)
            app.save()
        if id_status == "FAILED":
            app.update_status(15)
        app.kyc_validity_status = id_status
        app.save()

        print(f"RAN THE ID VALIDATION TASK with status {id_status}--> NAME CHECK INITIATED....")
        name_check_verification(app.application_id, check_type="id_check")


def id_validation_check(app) -> Tuple[str, list]:
    """kyc info validation"""
    id_messages = []
    id_status = "FAILED"
    kyc_info = update_id_type_key(app.applicant_kyc, app.guarantor_kyc)
    identities = []
    if kyc_info:
        for id_proof in kyc_info:
            try:
                personal_info = (
                    app.applicant_kyc
                    if id_proof["member"] == "borrower"
                    else app.guarantor_kyc
                )
                res = process_id_validation_result(id_proof, personal_info)
                if not res["data"]["validity"]:
                    id_status = "FAILED"
                    id_messages.append(f'{id_proof["id_type"]} validation failed.')
                    # return id_status, id_messages
                id_status = "SUCCESS"
                identities.append({"res": res, "id_proof": id_proof})
            except Exception as err:
                id_status = "PENDING"
                # return id_status, id_messages
    else:
        id_messages.append(
            f"ID Verification Failed, {' / '.join(ALLOWED_IDS)} Not Provided."
        )
    save_verified_data(app, identities)
    return id_status, id_messages


def process_id_validation_result(id_proof: dict, personal_info: object) -> dict:
    """
    Build request body for id validation and get the id validation data
    """
    data = build_id_request_body(id_proof, personal_info)
    res = fetch_id_validation_result(data)
    return res


def fetch_id_validation_result(
        request_body: dict,
        max_attempts: int = 5,
        retry_backoff: int = 1,
        retry_backoff_max: int = 2,
) -> dict:
    """
    Generate an ID Validation request and get the result

    :param request_body:
    :param profile:
    :param max_attempts: Number of attempts to make when fetching the result,
        defaults to 8
    :param retry_backoff: Number of seconds to backoff when retrying to get the
        result, defaults to 15
    :param retry_backoff_max: Max retry backoff
    :return: Dict containing the job result
    """

    try:
        m = get_mercury_client()
    except Exception:
        logger.warning(f"Unable to get mercury client")
        raise Exception("Unable to get mercury client")

    request_id = m.request_verify_id(**request_body)

    attempts = 0
    while attempts < max_attempts:
        request_id, result = m.get_verify_id_result(request_id)
        if result.get("status") == "SUCCESS":
            return result

        retry_backoff *= 1
        retry_backoff = (
            retry_backoff_max if retry_backoff > retry_backoff_max else retry_backoff
        )
        attempts += 1

    raise Exception(
        f"Error while getting ID verification response. Status:{result.get('status')}"
    )


def build_id_request_body(id_proof: dict, personal_info) -> dict:
    """
    Build request body of id proof
    """
    data = {
        "provider": "aadhaarapi",
        "profile": MERCURY_ID_VALIDATION_PROFILE,
        "id_type": id_proof["id_type"],
        "id_number": id_proof["id_value"],
    }
    if id_proof["id_type"] == "PASSPORT":
        passport_details = {
            "first_name": personal_info.name,
            "last_name": personal_info.last_name,
            "date_of_birth": datetime.strptime(personal_info.dob, "%d/%m/%Y"),
            "expiry_date": datetime.strptime(id_proof["id_valid_to"], "%d/%m/%Y"),
            "gender": personal_info.gender.upper(),
            "passport_type": "PERSONAL",
            "country": id_proof["country"],
        }
        passport_details = PassportDetails(**passport_details)
        data["passport_details"] = passport_details
    elif id_proof["id_type"] == "DRIVING_LICENSE":
        data["date_of_birth"] = datetime.datetime.strptime(personal_info.dob, "%d/%m/%Y")

    return data


def name_check_verification(app_id, check_type="bank_check"):
    app = EsthenosOrgApplication.objects.get(application_id=app_id)

    if check_type == "id_check" and app.kyc_validity_status == "SUCCESS":
        if app.name_match_percentage_id >= app.product.id_name_check_threshold:
            app.update_status(72)
            if app.get_previous_status(72) == 170:
                app.update_status(170)
        else:
            app.update_status(71)
            current_status = app.current_status.get("status_message")
            app.name_match_failure_reason.append(current_status)
            app.blocked_by_name_match = True
            app.save()

    elif check_type == "bank_check" and app.bank_info.is_verified:
        if app.name_match_percentage_bank >= app.product.bank_name_check_threshold:
            app.update_status(74)
            if app.get_previous_status(74) == 170:
                app.update_status(170)
        else:
            app.update_status(73)
            current_status = app.current_status.get("status_message")
            app.name_match_failure_reason.append(current_status)
            app.blocked_by_name_match = True
            app.save()


def update_id_type_key(applicant_kyc, guarantor_kyc) -> list:
    """
    Update the ID Validation specific keys for identities
    :param applicant_kyc:
    :param guarantor_kyc:
    :return:
    """
    data = []
    if applicant_kyc.id_type_one in ALLOWED_IDS and id_name_map.get(
            applicant_kyc.id_type_one, None
    ):
        data.append(
            {
                "id_type": id_name_map.get(applicant_kyc.id_type_one),
                "id_value": applicant_kyc.id_number_one,
                "id_valid_to": applicant_kyc.id_one_expiry_date,
                "member": "borrower",
                "id_num": 1,
            }
        )
    if applicant_kyc.id_type_two in ALLOWED_IDS and id_name_map.get(
            applicant_kyc.id_type_two, None
    ):
        data.append(
            {
                "id_type": id_name_map.get(applicant_kyc.id_type_two),
                "id_value": applicant_kyc.id_number_two,
                "id_valid_to": applicant_kyc.id_two_expiry_date,
                "member": "borrower",
                "id_num": 2,
            }
        )

    if guarantor_kyc.id_type_one in ALLOWED_IDS and id_name_map.get(
            guarantor_kyc.id_type_one, None
    ):
        data.append(
            {
                "id_type": id_name_map.get(guarantor_kyc.id_type_one),
                "id_value": guarantor_kyc.id_number_one,
                "id_valid_to": guarantor_kyc.id_one_expiry_date,
                "member": "co-borrower",
                "id_num": 1,
            }
        )
    if guarantor_kyc.id_type_two in ALLOWED_IDS and id_name_map.get(
            guarantor_kyc.id_type_two, None
    ):
        data.append(
            {
                "id_type": id_name_map.get(guarantor_kyc.id_type_two),
                "id_value": guarantor_kyc.id_number_two,
                "id_valid_to": guarantor_kyc.id_two_expiry_date,
                "member": "co-borrower",
                "id_num": 2,
            }
        )

    return data


def get_name_from_resp(response, id_type):
    """
    Given a response and an ID type, extract and return the name associated with that ID type in the response data.
    """
    name_key = {
        "PAN_CARD": "name_on_card",
        "DRIVING_LICENSE": "user_full_name",
        "VOTER_ID": "user_name_english",
    }.get(id_type)

    return response.get("data", {}).get("details", {}).get(name_key, "")


def remove_salutation(name):
    salutations = ["MR", "MS", "MRS", "MISS", "DR"]
    name_parts = name.split()
    for salutation in salutations:
        if salutation in name_parts:
            name_parts.remove(salutation)
    return " ".join(name_parts)


def name_matching_percentage(first_name="", second_name=""):
    """
    Compares two strings and returns their similarity.

    Using the Library which is under GNU General Public License v2.0

    Repo: https://github.com/seatgeek/thefuzz

    Algorithm: Levenshtein distance, for calculating the string matching percentage.

    Wiki: https://en.wikipedia.org/wiki/Levenshtein_distance
    """
    try:
        match_value = fuzz.token_sort_ratio(first_name, second_name)
    except:
        return 0
    return match_value


def save_verified_data(app, identities):
    """
    Storing the ID verified response for borrower and co-borrower
    """
    try:
        for identity in identities:
            id_proof, response, = identity["id_proof"], identity["res"]
            member = id_proof["member"]
            id_type = id_proof["id_type"]
            id_num = id_proof["id_num"]
            verified_name = get_name_from_resp(response, id_type)

            if member == "borrower":
                if id_num == 1:
                    app.applicant_kyc.id_one_verified_name = verified_name
                    print(f"THE VERIFIED NAME FOR BORROWER ID ONE = {verified_name}")
                elif id_num == 2:
                    app.applicant_kyc.id_two_verified_name = verified_name
                    print(f"THE VERIFIED NAME FOR BORROWER ID TWO = {verified_name}")

                # Checking the verified name and storing its matching percentage with the entered applicant name.
                raw_name = remove_salutation(app.applicant_kyc.name)
                app.name_match_percentage_id = name_matching_percentage(
                    raw_name, verified_name
                )
                app.applicant_kyc.save()
            elif member == "co-borrower":
                if id_num == 1:
                    app.guarantor_kyc.id_one_verified_name = verified_name
                    print(f"THE VERIFIED NAME FOR CO BORROWER ID ONE = {verified_name}")
                elif id_num == 2:
                    app.guarantor_kyc.id_two_verified_name = verified_name
                    print(f"THE VERIFIED NAME FOR BORROWER ID TWO = {verified_name}")

                app.guarantor_kyc.save()
            app.save()
    except Exception as e:
        pass
```