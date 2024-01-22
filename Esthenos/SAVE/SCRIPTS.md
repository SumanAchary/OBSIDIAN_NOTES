
## HOUSE IMAGES
```
# SCRIPT for House images.
def get_house_images_link(app_id, link=False):
    app = EsthenosOrgApplication.objects.get(application_id=app_id)
    if app.applicant_docs.personal_docs:
        for data in app.applicant_docs.personal_docs:
            if 'applicant_house_images' in data:
                if link:
                    return get_presigned_url(data,ExpiresIn=30*24*60*60)
                else:
                    return data
    return ''

data = []
branch_codes = []
bl = [EsthenosOrgBranch.objects.get(code=code) for code in branch_codes] 
apps = EsthenosOrgApplication.objects.filter(branch__in=bl,date_created__gte=datetime.datetime(2023,1,1)) 
failed = []
result = []
for app in apps:
    try:
				# IN case the data only needs populated links.
				link = get_house_images_link(app.application_id, True)
				if link:
			        details = [app.application_id,
			            app.branch.code,
			            app.branch.name,
			            get_house_images_link(app.application_id, True),
			            get_house_images_link(app.application_id),
			        ]
			        data.append(details)
    except Exception as e:
        failed.append([app,str(e),app.application_id])
result = []
headers = ['app_id', 'branch_code', 'branch_name', 'house_image_link', 'house_image_key']
result.append(headers)
result.extend(data)
user = EsthenosUser.objects.first()
writer = GzipLocalCsvWriter(filename, result)
report_path = writer.write()
send_attachment(report_path, 'housing', str(user.id))
delete_report_and_temp_dir(report_path)
```