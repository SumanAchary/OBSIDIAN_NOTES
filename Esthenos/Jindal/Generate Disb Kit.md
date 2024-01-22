
 

 disbursement_date = app.disbursement.disbursement_date
 date_str = disbursement_date.strftime("%d-%m-%Y")
 generate_disbursement_kit(app.application_id, date_str)
 get_presigned_url(app.disbursement.disbursement_pdf_link)