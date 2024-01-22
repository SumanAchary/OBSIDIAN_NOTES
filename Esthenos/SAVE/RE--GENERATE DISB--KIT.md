<hr>


```
from esthenos.tasks import *
group = EsthenosOrgGroup.objects.get(group_id = "ARG240111000001")
app = group.applications[0]
group = app.group
group.update_status(240)
app.update_status(240)
group_id = group.group_id
disbursement_date = group.disbursement_date
org_id = group.organisation.id
group.disbursement_status = DISBURSEMENT_STATES[1]
group.disbursement = False
group.disbursement_link = '#'
group.save()

applications = [
    app for app in group.applications if app.status in [233, 240, 242, 2401]
]
logger.info(f"apps count: {len(applications)}")

try:
    s3_key = ""
    if not group.is_mfi:
        s3_key = generate_disbursement_files(
            group, applications, disbursement_date, org_id
        )
        logger.info(f"{group.group_id} : {s3_key}")
except Exception as err:
    logger.info(f"{group_id} : {err}")
    group.disbursement_status = DISBURSEMENT_STATES[0]
    group.save()
    capture_exception()
else:
    group_disbursement_date = (
        group.disbursed_on
        if group.disbursed_on
        else datetime.datetime.now()
    )
    #  update the status to kit generated
    group.update_status(2402)
    for app in applications:
        org = app.organisation
        lms_client = org.get_lms_handler()
        # mark application as disbursed.
        app.disbursed(True, group_disbursement_date)
        app.save()
        group.center.timeslot = EsthenosOrgTimeSlot.objects.get(
            day=str(group.first_emi_date.day)
        )
        group.center.save()
        if app.is_mfi:
            create_lms_loan_application.delay(app.application_id)

    group.disbursed(s3_key)
    group.disbursement_status = DISBURSEMENT_STATES[2]

    group_disbursed_signal.send(group)
    group.save()
    logger.info(f"{group_id} disbursement done")

get_presigned_url(s3_key)
```