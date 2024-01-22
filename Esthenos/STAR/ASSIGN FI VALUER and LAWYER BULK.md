```
failed_apps = []
for app in applications:
    try:
        # lawyers
        # fields
        # valuers
        if not app.lawyer:
            initiate_lawyer(app)
        if not app.valuer:
            initiate_valuer(app)
        if not app.fi:
            initiate_fi(app)
        # if users not assigned
        if not app.lawyer.user:
            # GET LAWYER
            lawyer_id = get_lawyer_id(app)
            if lawyer_id:
                app.lawyer.user_id = lawyer_id
                app.save()
        if not app.valuer.user:
            # GET VALUER
            valuer_id = get_valuer_id(app)
            if valuer_id:
                app.valuer.user_id = valuer_id
                app.save()
        if not app.fi.user:
            # GET FI
            fi_id = get_fi_id(app)
            if fi_id:
                app.fi.user_id = fi_id
                app.save()
    except Exception as e:
        print(str(e))
        failed_apps.append(f"{app.application_id} {str(e)}")
```

<hr>

```
#Generate Report

result = []
headers = ["Application Id,"
           "Lawyer Name",
					 "Lawyer ID",
           "Valuer Name",
					 "Valuer ID",
           "FI Name",
					 "FI ID"
           "Branch Name",
           "Status"]
result.append(headers)
failed = []
for app in applications:
    try:
        app_id = app.application_id
        lawyer = app.lawyer.user.name if app.lawyer and app.lawyer.user else ""
        valuer = app.valuer.user.name if app.valuer and app.valuer.user else ""
        fi = app.fi.user.name if app.fi and app.fi.user else ""
        barcnh = app.branch.name
        status = app.status

        result.append([app_id, lawyer, valuer, fi, barcnh, status])
    except Exception as e:
        failed.append([app.application_id, str(e)])
```