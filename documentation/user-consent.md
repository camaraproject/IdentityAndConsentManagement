# User Consent Management

This document captures guidelines for Operator platform to handle user consents.

## Table of Contents

- [User Consent Management](#user-consent-management)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Glossary of Terms and Concepts](#glossary-of-terms-and-concepts)
  - [Purposes](#purposes)
    - [Purpose levels](#purpose-levels)
    - [Purpose relationship with other key concepts](#purpose-relationship-with-other-key-concepts)
    - [Purpose definition](#purpose-definition)
    - [Using purpose parameter in the authorization request](#using-purpose-parameter-in-the-authorization-request)
  - [User Identity](#user-identity)
  - [User Authentication/Authorization \& Consent Management](#user-authenticationauthorization--consent-management)
    - [Authentication mechanism/s](#authentication-mechanisms)
    - [Consent capture and storage](#consent-capture-and-storage)
    - [Athorization flows / grant types](#athorization-flows--grant-types)
      - [Authorization code flow](#authorization-code-flow)
      - [JWT-bearer flow](#jwt-bearer-flow)
      - [CIBA flow](#ciba-flow)
      - [Client Credentials flow](#client-credentials-flow)
      - [... (other grant types)](#-other-grant-types)
  - [Consent API](#consent-api)
  - [Annex: Flow source code](#annex-flow-source-code)


## Introduction

Operator platform implementing CAMARA should be built with a privacy-by-design approach to fully comply with data protection regulations like the [GDPR regulation](https://gdpr-info.eu/) in Europe, which provides a great level of protection for user's privacy. This means that an API that processes Personal Information needs a user consent. Consents are given by users to legal entities to process personal data under a specific purpose.

This document captures guidelines for Operator platform to handle user consents to comply with GDPR or equivalent requirements in an easy way, introducing the concept of purpose in OpenID Connect. Even being defined based on concepts that maps to GDPR regulation, proposed solution and concepts are generic enough to be used by Operators on any country.

>[TO BE EDITED/COMPLETED]

## Glossary of Terms and Concepts

The list below introduces several key concepts:

-	`Application`: client system that requires access to protected resources. Application must use the appropriate access token to access those resources (e.g. CAMARA Network APIs).
-	`User`: the human participant which is identified in Telco Operator by a unique user identifier (e.g. Subject identifier sub in OpenID Connect terminology). The user is the resource owner.
-	`Auth Server`: authorization server which receives requests from applications to issue an access token upon successful authentication and consent of the user. The OpenID Connect Provider is able to authenticate the user validating user identity against the corresponding Identity Provider. The authorization server exposes two endpoints: the Authorization endpoint and the Token endpoint.
-	`Identity Provider (IdP)`: It corresponds to the OpenID identity provider which is the party that provides user authentication as a service (it creates, maintains, and manages user identity information). 
-	`Resource Server`: A server that protects the user resources and receives access requests from applications. It accepts and validates an Access Token from the application and returns the appropriate resources to it.
-	`Scope`: OpenID Connect scope name which maps one or more resources. Some scopes may refer to Personal Information that could be affected by data protection regulations that require identifying the purpose for which they are requested.
-	`Data processing`: storing, transforming, or accessing Personal Information is considered processing data. Third party clients will be data processors, while the telco operator will be the data controller.
-	`Purpose`: The reason for which processing that Personal Information is required by the application. For example, an application might want to handle Personal Information to create a movie recommendation for a user. This is equivalent to the term Purpose mentioned in GDPR law; for example, [Art. 5 of the law](https://gdpr-info.eu/art-5-gdpr/) states the following: “Personal data shall be […] collected for specified, explicit and legitimate **purposes**”. Additionally, personal data is translated into Personal Information resources, as explained below.
-	`Personal Information Scope`: Also known as PI scopes, these are scopes that refer to resources providing access to Personal Information and thus requiring special protection. **Getting access to PI scopes must always be done by explicitly declaring a purpose**. Therefore, a Purpose maps to a predefined set of PI scopes, giving access to Personal Information processing.
-	`Consent`: an explicit opt-in action that the user takes to allow processing of their Personal Information. Consent can be a signature on a paper, a voice recording or clicking on an authorize button on a website. And it grants a **legal** entity (e.g., the operator or a specific third party) access to a set of **scopes** of a given **user**, under a specific **purpose**.
-	`Legal Entities`: are the legal subjects that are willing to get access to personal information with a specific, predefined purpose.

>[TO BE EDITED/COMPLETED]

## Purposes

A purpose declares what the application intends to do with a set of Personal Information resources. It provides a human-friendly description of why the data processing takes place and a list of the scopes of that Personal Information that gives access to its processing.

Purposes can be grouped into three categories: 

- **Automatic purpose** : This purpose does not require explicit consent from the user and cannot be revoked. For example, you need to handle call logs to create an invoice.
- **Opt-out purpose** : This purpose does not require explicit consent from the user, but consent can be revoked. For example, processing invoices to improve our commercial offering and for marketing purposes.
- **Opt-in purpose** : This purpose requires explicit user consent and can be revoked. For example, creating a credit score for a user based on CDRs and payment history.

Moreover, when it comes to granting consent, there are two types of the purposes:

-	**User-bound**: the consent is granted to be applied for all the identifiers of a given user i.e. granted at user level. For example, grant consent for accessing to the call history of any of her phone numbers. 
-	**Identifier-bound**: the consent is granted to be applied only for a specific user’s identifier i.e. for a public identifier associated with the service the Telco Operator provides to the user (typically a phone number). For example, grant consent for accessing to the call history of only one of her phone numbers.

>[TO BE EDITED/COMPLETED]

### Purpose levels

Depending on the purpose categories the purpose may have a specific level that determines the processing allowed by the user if the purpose is granted. **User consent is required to gain access to non-automatic purposes**.

| **Purpose Level** | Description | Category |
| --- | --- | --- |
|`legal_obligation` | A process that is required by law. | Automatic purpose |
|`contract` | A process that is needed to fulfill the service contract with the user, and it is already approved when the contract was signed. | Automatic purpose |
|`legitimate_interest`| An interest of the data processor that does not conflict with the fundamental interest of the individuals. | Opt-out purpose |
|`compatible` | A re-processing of data acquired through another purpose that is compatible with the first purpose. | Opt-out purpose|
|`consent` | This level applies to purposes which are unrelated enough to the service. | Opt-in purpose|
|`terms_and_conditions`| The operations or tasks done by an application to provide its functionality, as described in the Terms and Conditions. | Opt-in purpose|

>[TO BE EDITED/COMPLETED]

### Purpose relationship with other key concepts

The key concepts defined to handle user personal information are related to purpose concept in the following way:

![Key concepts relationship](./images/key-concepts-relationship.png)

- Scopes tell what resources (e.g. APIs) are accessible under those scopes.
- A purpose has associated a set of personal information scopes (PI scopes). 
  - These are scopes that refer to resources providing access to Personal Information and thus requiring special protection as stated by e.g. the GDPR law.
  - Getting access to PI scopes MUST always be done by explicitly declaring a purpose.
- Scopes are assigned to an application, granting access to APIs and other resources.
- Purposes are assigned to an application.

Therefore, an application will have access to:
  - APIs or resources covered by the assigned scopes
  - APIs or resources providing personal information under the assigned purpose (because of the PI scopes included in that purpose)
    - **Only** if user has granted her consent for that purpose
    - Which may happen automatically (automatic purposes) or may need an explicit user action to provide consent (depending on the purpose level of the purpose associated to that consent). In the second case, the consent is created and stored and will be validated during authorization.

>[TO BE EDITED/COMPLETED]

### Purpose definition

>[TO BE EDITED/COMPLETED] - purpose definition (naming + description) and format. Data Privacy Vocabulary: https://www.w3.org/community/dpvcg/​ | https://w3c.github.io/dpv/dpv/#vocab-purpose

### Using purpose parameter in the authorization request

The application can request a list of purposes in the authorization request of the [OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/rfc/rfc6749) using an additional `purpose` parameter:

| **Authorize request param** | Description |
| --- | --- |
| `purpose` | The value of this parameter is expressed as a list of space-delimited, case-sensitive strings. The strings will be the id of the purposes. |

This parameter has been defined in these guidelines to allow applications to provide a purpose/s name which represents the reason why a client application needs to process a certain piece of user Personal Information. Note that, as explained before, a purpose maps to a list of scopes giving access to Personal Information resources.

_Example: requesting "purpose\_1", "purpose\_2" and "purpose\_3"_

```
POST /bc-authorize HTTP/1.1
Authorization: Basic {Credentials}
Content-Type: application/x-www-form-urlencoded
purpose=purpose_1%20purpose_2%20purpose_3&
login_hint_token=eyJ[…].efg[…].asW[…]&
… 
```

The access token obtained from Token Endpoint will be granted to a subset or to all the scopes included in the requested purposes. The authorization server MAY fully or partially ignore the scopes of the requested purposes based on the authorization server policy. The authorization server MUST include the obtained scope list in the `scope` response parameter of the Token Endpoint to inform the client of the actual scopes granted.

The authorization server MUST include the obtained purpose list in the `purpose` response parameter of the Token Endpoint. The purpose MUST only be included in the response if at least one scope of the purpose has been granted.

_Example: only scopes of the "purpose\_1" and "purpose\_2" have been granted_

```
POST /token HTTP/1.1
Authorization: Basic {Credentials}
Content-Type: application/x-www-form-urlencoded
grant_type=urn%3Aopenid%3Aparams%3Agrant-type%3Aciba&
auth_req_id=1c266114-a1be-4252-8ad1-04986c5b9ac1
HTTP/1.1 200 OK
Content-Type: application/json;charset=UTF-8
{
  "access_token":"2YotnFZFEjr1zCsicMWpAA",
  "token_type":" Bearer",
  "expires_in":3600,
  "scope":"scope_1_of_purpose_1 scope_2_of_purpose_1 scope_1_of_purpose_2",
  "purpose":"purpose_1 purpose_2"
  …
}
```
The authorization endpoint still MUST support the `scope` parameter of the OAuth 2.0 specification. For example, to request an ID token or refresh token using OpenID Connect standard scopes or to request scopes not associated to Personal Information.

The application can request the Authorization endpoint using both parameters (`scope` and `purpose`), or one of them.

PI scopes cannot be requested using `scope` parameter. As stated before in this document, getting access to PI scopes must always be done by explicitly declaring a purpose. If a PI scope is requested by using the `scope` parameter, it will be ignored and removed from the request in Auth Server.

The authorization server MUST include all the obtained scopes and PI scopes in the `scope` response field of the Token Endpoint Request, the obtained scopes of the purposes and the obtained scopes of the `scope` parameter.

>[TO BE EDITED/COMPLETED]

## User Identity

>[TO BE EDITED/COMPLETED] - User Identification: Get an identifier of a user by some piece of information. E.g.: IP, phone number, document number...

## User Authentication/Authorization & Consent Management

>[TO BE EDITED/COMPLETED] - The base standard is OpenID Connect. User authentication: Verify that the user is who they say they are. For example: IP/Network Authentication, SMS OTP, User/Password, Push to Operator App... it depends on the type of authorisation (grant type) chosen according to the application use cases, integration model and requirements (B2B/B2C, via aggregator or not, delegated consent or not...). User authorisation: Verify access to the requested resource (OpenID Connect scope, purpose) through policies and rules... user consent is checked at this point as a pre-requisite before issuing an access token.

### Authentication mechanism/s

>[TO BE EDITED/COMPLETED] - Authentication mechanism/s to be considered... E.g.: IP/Network Authentication, SMS OTP, User/password, Push to Operator App​...

### Consent capture and storage

>[TO BE EDITED/COMPLETED] - Consent capture (by Operator vs by API Client a.k.a delegated) and storage (in Operator vs in a third party).

### Athorization flows / grant types

>[TO BE EDITED/COMPLETED] - Authorization flows / grant types to be considered... E.g.: Authorization code flow, JWT-bearer flow, CIBA flow, Client Credentials flow. For each API it should be possible to categorize it depending on the use case, integration/GTM model, personal information processing requirements, etc... and define the grant type/s to be used for each API.

#### Authorization code flow

>[TO BE EDITED/COMPLETED] - Flow to obtain an access token to consume Network API...

#### JWT-bearer flow

>[TO BE EDITED/COMPLETED] - Flow to obtain an access token to consume Network API...

#### CIBA flow

>[TO BE EDITED/COMPLETED] - Flow to obtain an access token to consume Network API...

#### Client Credentials flow

>[TO BE EDITED/COMPLETED] - Flow to obtain an access token to consume Network API...

#### ... (other grant types)

...

## Consent API

>[TO BE EDITED/COMPLETED] - Consent API: API to manage user consent, if required. For example when consent is captured by a third party and stored in Operator. A third-party can register the consent captured from a user for a specific purpose.

## Annex: Flow source code

>[TO BE EDITED/COMPLETED] - Source code (text format) of the flows described in this document.
