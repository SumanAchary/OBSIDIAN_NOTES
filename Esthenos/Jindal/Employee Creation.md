

```
import csv
import wtforms_json
from e_organisation.models import EsthenosOrgDealer, EsthenosUser, EsthenosOrgHierarchy, EsthenosOrgBranch, EsthenosOrg
from e_admin.forms import (
    AddOrganizationEmployeeForm,
)
from mappers.application.application_schema import DealersDetailsSchema

wtforms_json.init()

def read_csv_file(filename):
    with open(filename) as csvfile:
        lines = csvfile.readlines()
        first_item = lines[0]
        csvfile.seek(len(first_item))
        reader = csv.reader(csvfile, delimiter=",")
        for row in reader:
            print(row[4])
            hierarchy = EsthenosOrgHierarchy.objects.get(role=row[6])
            branches = EsthenosOrgBranch.objects.filter(code=row[14])
            payload ={
                "last_name_add_organisation":row[1],
                "first_name_add_organisation":row[0],
                "employee_id":row[4],
                "email_add_organisation":row[2],
                'date_of_birth_add_organisation':row[5],
                "active":True,
                "gender":"male",
                "date_of_joining":row[5],
                "password_add_organisation":row[3],
                "role":str(hierarchy.id),
                "address_add_org_emp":row[7],
                "city_add_organisation":row[10],
                "state_add_organisation":row[9],
                "country_add_organisation":row[8],
                "teleno_add_organisation":row[13],
                "tele_code_add_organisation":row[12],
                "postal_code_add_organisation":row[11],
                "states" : "",
                "areas" : "",
                "branches" :[str(branch.id) for branch in branches],
                "centers" : [],
                "notify_email":row[16]
            }
            emp_create(payload)


def emp_create(payload):
    try:
        employee = EsthenosUser.objects.filter(employee_id=payload["employee_id"])
        if employee:
            raise Exception("already exists")

        org = EsthenosOrg.objects.first()
        form = AddOrganizationEmployeeForm.from_json(payload)
        form.save(str(org.id))
        employee = EsthenosUser.objects.get(employee_id=payload["employee_id"])
        if employee.hierarchy.level >= 6:
            form = AddOrganizationEmployeeForm.from_json(payload)
            employee_after_update, errors = form.update(employee)
            employee.email = payload["email_add_organisation"]
            employee.update(set__email_confirmed=True)
            employee.active = True
            employee.save()
    except Exception as e:
        print(e,payload.get("employee_id")

for row in apps_ids:
    user:EsthenosUser = EsthenosUser.objects.get(employee_id=row[0])
    user.notify_email = row[1]
    user.active = True
    user.save()
```