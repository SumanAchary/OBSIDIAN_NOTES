mutual_agreement_doc_key

Mutual agreement needs a condition only download after e sign is complete.

@api_views.route("/zoop/return", methods=["POST"])
def zoop_webhook():

zoop response 
{
'success': True,
 'data': {'request_id': '6582b3a0f3ddac9daa805fae',
  'webhook_security_key': '3a456674-e3d4-4ff3-970e-56c9eb89a975',
  'expiry_time': '2023-12-20T09:38:00.104Z'},
 'status_code': 200
 }
https://save-dev.esthenos.com/applications/group/62fdead2e83af6754f57eb50
[AR230925000002](https://save-dev.esthenos.com/scrutiny/65116a4ae83af6fd149d482e)
----------------------------------------------------------------------------------------------


TODO --->
--> Need to introduce a new status for mutual doc e sign done.
--> Need to block the apps after one success or pending response from zoop after e_sign.
--> Make a separate method for generating request body for group and app.
--> 

@organisation_views.route("/group/doc_e_sign/<app_id>", methods=["GET"]) 




application_id = db.StringField(max_length=100, default="")  
request_id = db.StringField(max_length=100, default="")  
webhook_security_key = db.StringField(max_length=300, default="")  
expiry_time = db.StringField(max_length=100, default="")  
status = db.StringField(max_length=100, default="")

is_app_mutual_agreement_allowed

this is app level 

has_mutual_e_sign --> Key to show the download button.


co ordinates
def build_initialization_request_body_group(app_id: str, filename: str):

co-ordinates
0,0 --> bottom right

group = EsthenosOrgGroup.objects.get(id = '62fdead2e83af6754f57eb50')
group.zoop_details = []
group.save()

generate_mutual_agreement_document(group.group_id)

{'request_id': '658aa9cf33ec66d75d60c03f', 'success': True, 'response_code': '103', 'response_message': 'Partial Record Found', 'white_label': 'Y', 'metadata': {'billable': 'N', 'reason_code': 'UTP', 'reason_message': 'User terminated process'}, 'result': None, 'request_timestamp': '2023-12-26T10:24:15.259Z', 'response_timestamp': '2023-12-26T10:24:41.018Z'}