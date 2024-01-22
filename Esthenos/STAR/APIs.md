```
 BASE_URL = "https://starhousing-dev.esthenos.com";
 LOGIN = "/api/token/sourcing";
 USER_PROFILE = "/api/profile";
 USER_CHANGE_PASSWORD = "/api/v1/organisation/user/change_password";

 GET_REF_DATA = "/api/master_ref_data";
 GET_CATEGORY = "/api/organisation/employee-products";
 GET_DEALERS = "/api/organisation/dealers";
 GET_FREE_ALLOCATION_TIMESLOTS = "/api/v1/time_slots";

 FETCH_LEADS = "/api/leads/fetch";
 GET_REGISTRATION_READY = "/api/v1/organisation/registration-ready/applications";
 GET_RE_REGISTRATION_READY = "/api/v1/organisation/re-registration-ready/applications";
 GET_ON_HOLD_APPLICATIONS = "/api/v1/organisation/verification-on-hold/applications";
 GET_SCRUTINY_PASS = "/api/v1/organisation/scrutiny-pass/applications";
 GET_VALUATION_READY = "/api/organisation/valuer/fetch";
 GET_LUC_READY = "/api/v1/organisation/luc-ready/applications";
 GET_FEES_COLLECTION_READY = "/api/fee-collection-ready-apps";
 GET_DISBURSTMENT_READY = "/api/disbursement_ready_apps";
 GET_DISBURSTMENT_KIT_READY = "/api/disbursement_kit_generation_ready_apps";
 GET_SANCTION_FAILED = "/api/sanction-failed-apps";
 GET_FI_READY = "/api/organisation/fi/fetch";
 GET_APPLICATIONS_STATUS = "/api/organisation/applications";

 POST_PRE_REGISTRATION = "/api/organisation/applications/pre_register";
 POST_REGISTRATION = "/api/v1/organisation/application/{loan_category}";
 POST_RE_REGISTRATION = "/api/v1/organisation/application/re_register/{loan_category}";
 POST_PD = "/api/v1/organisation/applications/{loan_category}/verification";
 POST_ON_HOLD = "/api/v1/organisation/applications/{loan_category}/{app_id}/coborrowers";
 POST_VALUATION = "/api/organisation/valuer/submit";
 POST_LUC = "/api/v1/luc/submit";
 POST_FEES_COLLECTION = "/api/fee-collection-submission/{app_id}/";
 POST_DISBURSTMENT = "/api/e-mandate/{app_id}/";
 POST_DISBURSTMENT_KIT = "/api/generate-kit/{app_id}/";
 POST_FI = "/api/organisation/fi/submit";

 REQUEST_OTP = "/api/v1/contact/otp/generate";
 VERIFY_OTP = "/api/v1/contact/otp/verify";

 REQUEST_OKYC_OTP = "/api/okyc/otp";
 VERIFY_OKYC_OTP = "/api/okyc/otp/verify";

 GENERATE_QR_CODE = "/fee-collection/request_qr";
 GENERATE_PAYMENT_LINK = "/fee-collection/initiate_pg";
 CANCEL_PAYMENT = "/fee-collection/cancel_transactions/{app_id}/";
```