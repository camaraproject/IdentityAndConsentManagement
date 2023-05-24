# User Consent Management

This document defines guidelines for telco operator exposure platforms to manage CAMARA API access and user consent (when applicable).

## Table of Contents

- [User Consent Management](#user-consent-management)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Glossary of Terms and Concepts](#glossary-of-terms-and-concepts)
  - [Purposes](#purposes)
    - [Purpose levels](#purpose-levels)
    - [Purpose relationship with other key concepts](#purpose-relationship-with-other-key-concepts)
    - [Purpose definition](#purpose-definition)
    - [Applying purpose concept in the authorization request](#applying-purpose-concept-in-the-authorization-request)
  - [User Identity](#user-identity)
  - [User Authentication/Authorization \& Consent Management](#user-authenticationauthorization--consent-management)
    - [Authentication mechanism/s](#authentication-mechanisms)
    - [Consent capture and storage](#consent-capture-and-storage)
    - [Authorization flows / grant types](#authorization-flows--grant-types)
      - [Authorization code flow](#authorization-code-flow)
      - [JWT-bearer flow](#jwt-bearer-flow)
      - [CIBA flow](#ciba-flow)
      - [Client Credentials flow](#client-credentials-flow)
      - [... (other grant types)](#-other-grant-types)
  - [Consent API](#consent-api)
  - [Annex: Flow source code](#annex-flow-source-code)


## Introduction

Some APIs process personal information and require a “legal basis” to do so (e.g. “legitimate interest”, “contract”, “consent”, etc). Telco operator exposure platforms implementing CAMARA should be built with a privacy-by-design approach to fully comply with data protection regulations, such as the [GDPR regulation](https://gdpr-info.eu/) in Europe, to protect user privacy. This means that a CAMARA API exposed to capability consumers that processes personal data may require user consent (explicit user opt-in), depending on the "legal basis" for processing that data. This consent is given by users to legal entities to process personal data under a specific purpose.

As per CAMARA commonalities ["Authentication and Authorization Concept for Service APIs"](https://github.com/camaraproject/WorkingGroups/blob/main/Commonalities/documentation/CAMARA-AuthN-AuthZ-Concept.md), CAMARA API access will be secured using [OpenID Connect](https://openid.net/connect/) on top of OAuth 2.0 protocol. This document defines guidelines for operator exposure platform to manage CAMARA API access and user consent to comply with GDPR or equivalent requirements in an easy way, introducing the concept of purpose in OpenID Connect. Even being defined based on concepts that maps to GDPR regulation, proposed solution and concepts are generic enough to be used by Operators on any country.

The document details aspects regarding the consent management, which includes following concepts:

- CRUD operations for a consent, i.e.: create, read, update or delete a consent record. Specific details will be handled as for example in fact consents may not be deleted but revoked instead.
- What information is relevant in the managed consent record, e.g.: end user identifier to which the consent is associated, expiration/validity, etc.
- Policy enforcement to validate existence and validity of consent before authorizing access to a (set of) scopes.
- Allow end users to view and update their consents.
- Flows detailing how consent can be collected from the user without degrading the user experience while using a third-party service in an application.

>[TO BE EDITED/COMPLETED]

## Glossary of Terms and Concepts

The list below introduces several key concepts:

-	`Application`: client system that requires access to protected resources. Application must use the appropriate access token to access those resources (e.g. CAMARA Network APIs).
-	`User`: the human participant which is identified in telco operator by a unique user identifier (e.g. subject identifier `sub` in OpenID Connect terminology). The user is the resource owner.
-	`Auth Server`: authorization server which receives requests from applications to issue an access token upon successful authentication and consent of the user. The OpenID Connect provider is able to authenticate the user validating user identity against the corresponding identity provider. The authorization server exposes two endpoints: the authorization endpoint and the token endpoint.
-	`Identity Provider (IdP)`: It corresponds to the OpenID identity provider which is the party that provides user authentication as a service (it creates, maintains, and manages user identity information). 
-	`Resource Server`: A server that protects the user resources and receives access requests from applications. It accepts and validates an access token from the application and returns the appropriate resources to it.
-	`Scope`: OpenID Connect scope name which maps one or more resources. Some scopes may refer to personal information that could be affected by data protection regulations that require identifying the purpose for which they are requested.
-	`Data processing`: storing, transforming, or accessing personal information is considered processing data. Third party clients will be data processors, while the telco operator will be the data controller.
-	`Purpose`: The reason for which processing that personal information is required by the application. For example, an application might want to handle personal information to create a movie recommendation for a user. This is equivalent to the term purpose mentioned in GDPR law; for example, [Art. 5 of the law](https://gdpr-info.eu/art-5-gdpr/) states the following: “Personal data shall be […] collected for specified, explicit and legitimate **purposes**”. Additionally, personal data is translated into personal information resources, as explained below.
-	`Personal Information Scope`: Also known as PI scopes, these are scopes that refer to resources providing access to personal information and thus requiring special protection. **Getting access to PI scopes must always be done by explicitly declaring a purpose**. Therefore, a purpose maps to a predefined set of PI scopes, giving access to personal information processing.
-	`Consent`: an explicit opt-in action that the user takes to allow processing of their personal information. It grants a **legal** entity (e.g., the operator or a specific third party) access to a set of **scopes** of a given **user**, under a specific **purpose**.
-	`Legal Entities`: are the legal subjects that are willing to get access to personal information with a specific, predefined purpose.

>[TO BE EDITED/COMPLETED]

## Purposes

A purpose declares what the application intends to do with a set of personal information resources. It provides a human-friendly description of why the data processing takes place and a list of the scopes of that personal information that gives access to its processing.

Purposes can be grouped into three categories*: 

- **Automatic purpose** : This purpose does NOT require explicit opt-in from the user and cannot be revoked. For example, you need to handle call logs to create an invoice.
- **Opt-out purpose** : This purpose does not require explicit opt-in from the user, but consent can be revoked. For example, processing invoices to improve our commercial offering and for marketing purposes.
- **Opt-in purpose** : This purpose requires explicit user opt-in and can be revoked. For example, creating a credit score for a user based on CDRs and payment history.

_(*)NOTE: Some categories may NOT be allowed for all 3rd party Apps. (e.g. Automatic purpose)_

The explicit opt-int of the user can be collected either for all their identifiers or only for certain identifiers of the user. Accordingly, there are two types of purposes::

-	**User-bound**: the consent is granted to be applied for all the identifiers of a given user i.e. granted at user level. For example, grant consent for accessing to the call history of any of their phone numbers. 
-	**Identifier-bound**: the consent is granted to be applied only for a specific user’s identifier i.e. for a public identifier associated with the service the telco operator provides to the user (typically a phone number). For example, grant consent for accessing to the call history of only one of their phone numbers.

>[TO BE EDITED/COMPLETED]

### Purpose levels

Depending on the purpose categories the purpose may have a specific level that determines the processing allowed by the user if the purpose is granted. **User consent is required to gain access to non-automatic purposes**.

| **Purpose Level** | Description | Category |
| --- | --- | --- |
|`legal_obligation` | A process that is required by law. | Automatic purpose |
|`contract` | A process that is needed to fulfill the service contract with the user, and it is already approved when the contract was signed. | Automatic purpose |
|`legitimate_interest`| An interest of the data processor that does not conflict with the fundamental interest of the individuals. | Opt-out purpose |
|`consent` | This level applies to purposes which are unrelated enough to the service. | Opt-in purpose|

>[TO BE EDITED/COMPLETED]

### Purpose relationship with other key concepts

The key concepts defined to handle user personal information are related to purpose concept in the following way:

![Key concepts relationship](./images/key-concepts-relationship.png)

- Scopes tell what resources (e.g. APIs) are accessible under those scopes.
- A purpose has associated a set of personal information scopes (PI scopes). 
  - These are scopes that refer to resources providing access to personal information and thus requiring special protection as stated by e.g. the GDPR law.
  - Getting access to PI scopes MUST always be done by explicitly declaring a purpose.
- Purposes are assigned to an application.

Therefore, an application will have access to:
  - APIs or resources providing personal information under the assigned purpose (because of the PI scopes included in that purpose)
    - **Only** if user has granted their consent for that purpose
    - Which may happen automatically (automatic purposes) or may need an explicit user action to provide consent (depending on the purpose level of the purpose associated to that consent). In the second case, the consent is created and stored and will be validated during authorization.

>[TO BE EDITED/COMPLETED]

### Purpose definition

>[TO BE EDITED/COMPLETED] - purpose definition (naming + description) and format. Data privacy vocabulary: https://www.w3.org/community/dpvcg/​ | https://w3c.github.io/dpv/dpv/#vocab-purpose

### Applying purpose concept in the authorization request

>[TO BE EDITED/COMPLETED - [OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/rfc/rfc6749)]

## User Identity

>[TO BE EDITED/COMPLETED] - User identification: Get an identifier of a user by some piece of information. E.g.: IP, phone number, document number...

## User Authentication/Authorization & Consent Management

>[TO BE EDITED/COMPLETED] - The base standard is OpenID Connect. User authentication: Verify that the user is who they say they are. For example: IP/Network authentication, SMS OTP, User/Password, Push to operator application... it depends on the type of authorization (grant type) chosen according to the application use cases, integration model and requirements (B2B/B2C, via aggregator or not, delegated consent or not...). User authorization: Verify access to the requested resource (OpenID Connect scope, purpose) through policies and rules... user consent is checked at this point as a pre-requisite before issuing an access token.

### Authentication mechanism/s

>[TO BE EDITED/COMPLETED] - Authentication mechanism/s to be considered... E.g.: IP/Network authentication, SMS OTP, User/password, Push to operator application​...

### Consent capture and storage

>[TO BE EDITED/COMPLETED] - Consent capture (by operator vs by API Client a.k.a delegated) and storage (in operator vs in a third party).

### Authorization flows / grant types

>[TO BE EDITED/COMPLETED] - Authorization flows / grant types to be considered... E.g.: Authorization code flow, JWT-bearer flow, CIBA flow, Client credentials flow. For each API it should be possible to categorize it depending on the use case, integration/GTM model, personal information processing requirements, etc... and define the grant type/s to be used for each API.

#### Authorization code flow

>[TO BE EDITED/COMPLETED] - Flow to obtain an access token to consume network API...

#### JWT-bearer flow

>[TO BE EDITED/COMPLETED] - Flow to obtain an access token to consume network API...

#### CIBA flow

>[TO BE EDITED/COMPLETED] - Flow to obtain an access token to consume network API...

#### Client Credentials flow

>[TO BE EDITED/COMPLETED] - Flow to obtain an access token to consume network API...

#### ... (other grant types)

...

## Consent API

>[TO BE EDITED/COMPLETED] - Consent API: API to manage user consent, if required. For example when consent is captured by a third party and stored in operator. A third-party can register the consent captured from a user for a specific purpose.

## Annex: Flow source code

>[TO BE EDITED/COMPLETED] - Source code (text format) of the flows described in this document.
