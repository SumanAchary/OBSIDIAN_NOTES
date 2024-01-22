<hr>


```
{
        "bank_details": {
            "bank_name": $('#bank_name').val(),
            "bank_branch": $('#bank_branch').val(),
            "bank_ifsc_code": $('#bank_ifsc_code').val(),
            "bank_account_type": $('#bank_account_type').val(),
            "bank_account_number": $('#bank_account_number').val(),
            "bank_account_holder_name": $('#bank_account_holder_name').val(),
	            "bank_account_operational_since": "{{data['bank_details']['bank_account_operational_since']}}",
            "bank_code": $('#bank_code').val(),
            "branch_code": $('#branch_code').val(),
            "mobile_no": $('#mobile_no').val(),
            "bank_qc_discrepancy": $('#bank_details_update').val()
        },
        "applicant_kyc": {
            "id_number_one": "",
            "id_type_one": "{{data['applicant_id_one']['id_name']}}",
            "id_number_two": "",
            "id_type_two": "{{data['applicant_id_two']['id_name']}}",
            "id_one_data_qc_discrepancy": "",
            "id_one_image_qc_discrepancy": $('#app_id_one_image_qc_discrepancy').val(),
            "id_one_image_qc_description": $('#app_id_one_image_qc_description').val(),
            "id_two_data_qc_discrepancy": "",
            "id_two_image_qc_discrepancy": $('#app_id_two_image_qc_discrepancy').val(),
            "id_two_image_qc_description": $('#app_id_two_image_qc_description').val(),
        },
        "guarantor_kyc": {
            "id_number_one": "",
            "id_type_one": "{{data['guarantor_id_one']['id_name']}}",
            "id_number_two": "",
            "id_type_two": "{{data['guarantor_id_two']['id_name']}}",
            "id_one_data_qc_discrepancy": "",
            "id_one_image_qc_discrepancy": $('#co_app_id_one_image_qc_discrepancy').val(),
            "id_one_image_qc_description": $('#co_app_id_one_image_qc_description').val(),
            "id_two_data_qc_discrepancy": "",
            "id_two_image_qc_discrepancy": $('#co_app_id_two_image_qc_discrepancy').val(),
            "id_two_image_qc_description": $('#co_app_id_two_image_qc_description').val(),
        },
        "applicant_profile_image": {
            "applicant_profile_image_qc_discrepancy": $('#applicant_profile_image_qc_discrepancy').val(),
            "applicant_profile_image_qc_description": $('#applicant_profile_image_qc_description').val(),
        },
        "co_applicant_profile_image": {
            "co_applicant_profile_image_qc_discrepancy": $('#co_applicant_profile_image_qc_discrepancy').val(),
            "co_applicant_profile_image_qc_description": $('#co_applicant_profile_image_qc_description').val(),
        },
        "qc_status": $('#qc_status').val()
    }
    
```



THIS IS WHAT WE NEED FOR MOBILE
```
applicant_docs = data.get("applicant_docs_discrepancy", {})
app.applicant_docs.qc_discrepancy["personal_docs_discrepancy"] = applicant_docs.get(
        "personal_docs_discrepancy", []
    )
app.applicant_docs.qc_discrepancy["personal_docs_reasons"] = applicant_docs.get(
        "personal_docs_reasons", []
    )
    app.applicant_docs.qc_discrepancy["personal_docs_description"] = applicant_docs.get(
        "personal_docs_description", []
    )
```



# QC Flow and APIs

```
API for QC ready apps:

`application_qc_ready ---function`

`/application/qc_ready`

only apps with the status code 320 would be visible.

Feature is `features_qc_operations`

`@GET("/api/v2/organisation/details/{application_id}")` here you add the QC discrepancy key.

`@POST("/api/organisation/applications/verification/{application_id}")` This would be the POST api format from mobile QC update submit.

`@GET("/api/v2/organisation/groups/{group_id}/applications/cv")` Follow this for QC Mobile GET API structure.

Have created this API.
/api/v2/organisation/qc_discrepancy/<center_id>/

POST API
/api/qc_update/<app_id>
```



# APP for testing.
TESTING APP

```
AR230913000002
```
