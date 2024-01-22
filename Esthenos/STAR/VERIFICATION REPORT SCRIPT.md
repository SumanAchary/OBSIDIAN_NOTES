```
import json
from e_organisation.models import EsthenosOrgApplication, EsthenosOrgApplicationStatusType
import datetime

from esthenos.services.email_service import send_email
from esthenos.services.id_validation.requests import update_id_type_key
from esthenos.settings import ID_TYPES

status_code = 15
apps = EsthenosOrgApplication.objects.all()
status:EsthenosOrgApplicationStatusType = EsthenosOrgApplicationStatusType.objects.get(
            status_code=status_code
        )
result = []
failed = []
headers = "Application Id,Date Time,Current Status,IDs"
result.append(headers)
for app in apps.no_cache():
    try:
        # print(app.application_id)
        for stats in app.timeline:
            if stats.get('status_code') == status_code:
                if status_code == 15:
                        ids = []
                        for i, id_kyc in enumerate(app.applicant_details.kyc_info):
                            id_doc = json.loads(id_kyc.to_json())
                            id_proof = update_id_type_key(id_doc)
                            if id_proof["id_type"] in ID_TYPES:
                                ids.append(id_proof["id_type"])
                result.append(f"{app.application_id},{stats.get('updated_on').strftime('%d %B %Y')},{app.timeline[-1].get('status')},{ids}")
    except:
        failed.append(app.application_id)
    
send_email(status.status.replace("_"," "),"\n".join(list(map(str,result))),recipients=['suman@esthenos.com','guruprasad@esthenos.com'])
```