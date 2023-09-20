


# Purpose, API & open Gateway product granularity

## Objectives of this document

A lot of discussion around the concept of purpose and its management in CAMARA API occurred in particular in the Issue#32 (https://github.com/camaraproject/IdentityAndConsentManagement/issues/32).

We have, as of now, 2 distinct proposal on the table: one initially provided by Telefonica, and then a second proposed by the Orange team. Both proposal have a lot of common & agreed on the strong requirement for purpose & consent management. But as both proposal diverge on the Open Gateway product whom frame the API commercialization, the following table sum-up main points of both proposal in a form of a FAQ.

## Proposal presentation

| Topic     | Telefonica proposal | Orange proposal |
| --------- | ------------------- | --------------- |
| What is the Open Gateway (OPGW) Product granularity? |  |  Open Gateway product matches 1-1 with a CAMARA API. For example, as of now, the CAMARA Device location API family features 2 APIs (2 yaml): `location-retrieval` and `location_verification` : we consider them as 2 separate OPGW product. The OPGW product covers completely the API (all the resources of the API). This is an important point because it means that API granularity must not be considered only from a technical aspect but also from a business aspect (which is already the case for CAMARA API). <br> The OPGW product is **not** strong-coupled with a purpose but independent of it. However in the catalog it is possible to recommend OPGW product with purpose (see next point).|
| Do link between OPGW product and purpose is visible in Telco Catalog? |  |  In the catalog, it could be possible to browse OPGW product per category. Several categories could be created, as such 'purpose' could be defined as a category, and purpose  value could be defined as category value (in order to get for example OPGW Product related to Fraud prevention & detection) but this is only informative information.  |
| What are country market required agreement before to launch?|   | Telco must agree on the OPGW product to be launched in the country as a minimum 'critical mass' must be available. Additionally Telco must indicate for each API scope the legal base associated with purposes for this country.|
| What is expected during Application onboarding?|   | When a developer wishes to uses CAMARA API, developer first onboards an application. Mandatory information to onboard an application is to provide the purpose (or the list of purposes) associated with this application. |
| What is expected for each `/authorize` call? |   | For each `/authorize` call the `scope` attribute **must** be valued. in Orange proposal, this scope is valued by a concatenation of purpose+technical scope. This is straightforward for the developer to build it as: <br> - the purpose is known from the app onboarding (see previous point)  <br> - the technical scope is  defined in each CAMARA API yaml in the `security` property at path/method level. <br> Note: For one `/authorize` it is valid to ask for several technical scopes like for example `scope=FraudDetectionAndPrevention:simswap-verify-read  number-verification-verify-read`. This is a recommended pattern to limit interaction with end user. |
| How the legal consent displayed to the end user is defined and provided? | | Telco  associates a legal wording for each scope design-time. Then on the fly the legal notice is generated and displayed to the front end. <br> <br > As an example, simswap-verify-read technical scope is associated to wording:  'Verify latest SIM swap Date', number-verification-verify-read to 'Verify your MSIDN from network'. The request `POST /authorize` with `scope=FraudDetectionAndPrevention:simswap-verify-read` triggers following notice: <br> _"For fraud detection and prevention, application NiceApp request your consent to perform following action(s): <br> - Verify latest SIM swap Date <br> - Verify your MSIDN from network"_
| What if a new API is introduced in a country? |  | As long as a OPGW product is already existing for this API there is nothing else to do. This OPGW product should be visible in the catalogue once Telco has a live implementation of this API |
| What if a developper wishes to use API for a purpose not defined in this country? |  | Nothing to be done at OPGW product. The only action is at the authorization server to implement the legal base for this purpose for the API scope(s). |











