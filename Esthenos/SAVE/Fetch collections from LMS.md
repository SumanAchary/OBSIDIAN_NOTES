```
collection_due_date = datetime.datetime.now()

upload_demand_from_lms_task.delay(collection_due_date.strftime("%d %B %Y"))

colls = EsthenosOrgCollection.objects.filter(collection_due_date=datetime.datetime(2023,1,17),customer_loan_no='AR0020000141')

	Coll.collector_code

user = EsthenosUser.objects.get(usr_id=909)

for coll in colls:
    coll.collector_code = user.usr_id
    coll.collector_name= user.name
    coll.product_code = '1'
    coll.save()

```