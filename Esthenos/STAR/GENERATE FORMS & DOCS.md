
```
key = "loan_application_form"
app_id = "STARAPP230426000001"


def generate_forms_and_docs_adhoc(app_id, key):
    try:
        app = EsthenosOrgApplication.objects.get(application_id=app_id)
    except:
        print("ERROR, APP NOT FOUND!!!!")
    else:
            if key == "loan_application_form":
                generate_loan_form(app.application_id, "loan_application_form")
                print(f"\n\nNEW GENERATED {key.upper()} SUCCESFULLY.........;D")
            elif key == "cam_sheet":
                generate_cam_sheet(app_id)
                print(f"\n\nNEW GENERATED {key.upper()} SUCCESFULLY......;D")
            elif key == "loan_agreement":
                generate_loan_form(app.application_id, "loan_agreement")
                print(f"\n\nNEW GENERATED {key.upper()} SUCCESFULLY................. ;D")
        else:
            print(
                f"For {app} the {key} document is not present in the application. You can directly regenerate after manual "
                f"intervention.")

            str = input("Say YES or NO to ReGenerate\n")
            if str.lower() == 'yes':
                if key == "loan_application_form":
                    generate_loan_form(app.application_id, "loan_application_form")
                    print(f"\n\nGENERATED {key.upper()} SUCCESFULLY.........;D")
                elif key == "cam_sheet":
                    generate_cam_sheet(app_id)
                    print(f"\n\nGENERATED {key.upper()} SUCCESFULLY......;D")
                elif key == "loan_agreement":
                    generate_loan_form(app.application_id, "loan_agreement")
                    print(f"\n\nGENERATED {key.upper()} SUCCESFULLY................. ;D")
```