```
"code": 404,
"message": "{\"code\":404,\"message\":\"NOT FOUND\",\"data\":{\"message\":\"Report ID 2516425 does not exists\"}}",
"request": {
"code": "S001",
"message": "Digital Property Search Report uploaded",
"query_id": "64e5d0fd073938eda4004a83",
"report_id": 2516425,
"reference_data": {
"case_number": "STAR230823000005",
"notes": "Need to process asap.",
"lookback_period": 13
}
```


This report ID: 2516425 sent by Teal does not exist in our system as "case_number": "STAR230823000005" has the report ID: 2516426. Due to which our API threw an Exception as it is not able to find that report_id in our system for any case number. Please look into this.