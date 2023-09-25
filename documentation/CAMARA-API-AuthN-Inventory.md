## CAMARA-API-AuthN-Inventory.md

| API Family | API Name | Scopes (not needed for oauth2-cc) | Security-scheme name | Type | Recommended Flow |
| -------- | -------- | ------ | ------------- | ---- | ---- |
|| qod| qod-sessions-read, qod-sessions-write, qod-sessions-delete, qod-profiles-read |oauth2 | oauth2 | clientCredentials |
|| number-verification |number-verification-verify-read, number-verification-share-read | openId | openIdConnect | authorizationCode |
|| device-status |xxxx (To be added)| openId | openIdConnect | ciba |
| DeviceLocation | location-verification |xxxx (To be added)| openId | openIdConnect | ciba |
| DeviceLocation | location-retrieval |xxxx (To be added)| openId | openIdConnect | ciba |
|| device-identifier ||oauth2 | oauth2 | clientCredentials |
| OTPValidation | one-time-password-sms||oauth2 | oauth2 | clientCredentials |
|  CarrierBillingCheckOut| carrier-billing-payments| carrier-billing-payments-create, carrier-billing-payments-prepare, carrier-billing-payments-confirm, carrier-billing-payments-cancel, carrier-billing-payments-read | openId | openIdConnect | authorizationCode |


