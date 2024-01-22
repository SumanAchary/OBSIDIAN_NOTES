```
from e_organisation.models import EsthenosOrgApplication, EsthenosOrgApplicationStatusType
import datetime

from esthenos.services.email_service import send_email

status_code = 147
apps = EsthenosOrgApplication.objects.all()
status:EsthenosOrgApplicationStatusType = EsthenosOrgApplicationStatusType.objects.get(
            status_code=status_code
        )
result = []
failed = []
headers = "Application Id,Date Time,Current Status"
result.append(headers)
for app in apps.no_cache():
    try:
        # print(app.application_id)
        for stats in app.timeline:
            if stats.get('status_code') == status_code:
                result.append(f"{app.application_id},{stats.get('updated_on').strftime('%d %B %Y')},{app.timeline[-1].get('status')}")
    except:
        failed.append(app.application_id)
    
send_email(status.status.replace("_"," "),str(result),recipients=['suman@esthenos.com','guruprasad@esthenos.com'])
```