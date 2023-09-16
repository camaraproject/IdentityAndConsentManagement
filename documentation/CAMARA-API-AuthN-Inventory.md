## CAMARA-API-AuthN-Inventory.md

| API Family | API Name | Scopes (not needed for oauth2-cc) | Security-scheme name | Type | Flow |
| -------- | -------- | ------ | ------------- | ---- | ---- |
|| qod| qod-sessions-read, qod-sessions-write, qod-sessions-delete, qod-profiles-read |oauth2-cc | oauth2 | clientCredentials |
|| number-verification |number-verification-verify-read, number-verification-share-read | openId-ac | openIdConnect | authorizationCode |
|| device-status |xxxx (To be added)| openId-cb | openIdConnect | ciba |
| DeviceLocation | location-verification |xxxx (To be added)| openId-cb | openIdConnect | ciba |
| DeviceLocation | location-retrieval |xxxx (To be added)| openId-cb | openIdConnect | ciba |
|| device-identifier ||oauth2-cc | oauth2 | clientCredentials |
| OTPValidation | one-time-password-sms||oauth2-cc | oauth2 | clientCredentials |
|  CarrierBillingCheckOut| carrier-billing-payments| carrier-billing-payments-create, carrier-billing-payments-prepare, carrier-billing-payments-confirm, carrier-billing-payments-cancel, carrier-billing-payments-read | openId-ac | openIdConnect | authorizationCode |


