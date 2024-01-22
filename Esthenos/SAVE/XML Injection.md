
```

with open('xml_sample5.xml','r') as f:
        raw_cb_report = f.read()


#update it in the app.

response_p = ET.fromstring(raw_cb_report)
app.highmark_response = raw_cb_report
app.highmark_key = upload_raw_cb_report(app, "applicant")
app.highmark_response = ""
app.update_cashflow(response_p)
temp = pr.get_valules_from_highmark_response(response_p)
highmark_response = EsthenosOrgApplicationHighMarkResponse(
	national_id_card=get_national_id_card(response_p),
	num_active_account=get_num_active_account(response_p),
	sum_overdue_amount=0,
	indv_response_list=temp[0],
	total_loan_balance=temp[1],
	total_dpd_count=temp[2],
	write_off_loan_twelve_months=write_off_loan_12M(response_p),
)
highmark_response.save()
highmark_response.indv_response_list = temp[0]
highmark_response.toal_loan_balance = temp[1]
highmark_response.total_dpd_count = temp[2]
highmark_response.save()
app.highmark_response1 = highmark_response
app.save()
```