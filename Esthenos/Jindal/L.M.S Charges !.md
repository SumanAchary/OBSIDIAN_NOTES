```
def build_loan_charges(app: object):  
    """  
    builds loan charges    """    all_charges = []  
    for charges in app.product.charges:  
        if getattr(app.fee_collection, charges.name, 0):  
            all_charges.append(  
                {  
                    "chargeId": charges.charge_id,  
                    "amount": getattr(app.fee_collection, charges.name, 0),  
                }  
            )  
    return all_charges
```


```
def build_loan_application_request(  
    app, split: bool = False, sub_product_index: int = 0  
):  
    """  
    build request body for create loan application in mifos    :param app:    :param client_id:    :return:    """    expected_disbursement_date = app.id.generation_time + datetime.timedelta(days=2)  
    external_id = app.application_id  
    product = app.product  
    principal = app.approved_loan_details.loan_recommended  
    interest_rate = (  
        app.approved_loan_details.irr  
        if app.loan_category == "TWO_WHEELER"  
        else app.approved_loan_details.interest_rate  
    )  
    if split:  
        external_id += "_" + str(sub_product_index)  
        principal = get_sub_product_loan_amount(app, principal, sub_product_index)  
        product = app.product.co_lending_split[sub_product_index].product  
        interest_rate = (  
            calculate_interest_rate_partner_1(app, principal)  
            if sub_product_index == 0  
            else app.product.rate_of_interest_partner_2  
        )  
    emi_amount = app.emi_amount_as_per_emi_calc(  
        split, sub_product_index=sub_product_index  
    )  
    charges = build_loan_charges(app) if not split else []  
    payload = {  
        "dateFormat": "dd MMMM yyyy",  
        "locale": "en",  
        "productId": int(product.lms_product_id),  
        "clientId": int(app.lms_customer_id),  
        "externalId": external_id,  
        "principal": principal,  
        "loanType": "individual",  
        "loanTermFrequency": app.approved_loan_details.tenure,  
        "loanTermFrequencyType": 2,  
        "numberOfRepayments": app.approved_loan_details.tenure,  
        "repaymentEvery": 1,  
        "repaymentFrequencyType": 2,  
        "interestRatePerPeriod": interest_rate,  
        "amortizationType": 1,  
        "interestType": 0,  
        "interestCalculationPeriodType": 1,  
        "expectedDisbursementDate": expected_disbursement_date.strftime("%d %B %Y"),  
        "submittedOnDate": app.id.generation_time.strftime("%d %B %Y"),  
        "transactionProcessingStrategyId": 2,  
        "daysInYearType": 1,  
        "fixedEmiAmount": emi_amount,  
        "charges": charges,  
    }  
  
    return payload
```




