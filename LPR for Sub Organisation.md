```
from esthenos.tasks import *

category='all'

user=EsthenosUser.objects.first()

user_id=user.id

start_date='8-4-2023'
end_date='11-12-2023'
start_date=datetime.datetime.strptime(start_date,"%d-%m-%Y")
end_date=datetime.datetime.strptime(end_date,"%d-%m-%Y")
report_path = lpr_report_optimized(start_date, end_date, user_id, category)
```
