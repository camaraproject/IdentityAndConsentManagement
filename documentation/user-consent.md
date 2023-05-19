# User Consent Management

This document defines guidelines for Telco Operator Exposure platforms to manage CAMARA API access and user consent (when applicable).

## Table of Contents

- [User Consent Management](#user-consent-management)
  - [Table of Contents](#table-of-contents)
  - [Introduction](#introduction)
  - [Glossary of Terms and Concepts](#glossary-of-terms-and-concepts)
  - [Purposes](#purposes)
    - [Purpose levels](#purpose-levels)
    - [Purpose relationship with other key concepts](#purpose-relationship-with-other-key-concepts)
    - [Purpose definition](#purpose-definition)
    - [Applying purpose concenpt in the authorization request](#applying-purpose-concenpt-in-the-authorization-request)
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

Some APIs process personal information and require a “legal basis” to do so (e.g. “legitimate interest”, “contract”, “consent”, etc). Telco Operator Exposure platforms implementing CAMARA should be built with a privacy-by-design approach to fully comply with data protection regulations, such as the [GDPR regulation](https://gdpr-info.eu/) in Europe, to protect user privacy. This means that a CAMARA API exposed to Capability Consumers that processes personal data may require user consent (explicit user opt-in), depending on the "legal basis" for processing that data. This consent is given by users to legal entities to process personal data under a specific purpose.

As per CAMARA Commonalities ["Authentication and Authorization Concept for Service APIs"](https://github.com/camaraproject/WorkingGroups/blob/main/Commonalities/documentation/CAMARA-AuthN-AuthZ-Concept.md), CAMARA API access will be securitized using [OpenID Connect](https://openid.net/connect/) on top of OAuth 2.0 protocol. This document captures guidelines for Operator Exposure platform to manage CAMARA API access and user consent to comply with GDPR or equivalent requirements in an easy way, introducing the concept of purpose in OpenID Connect. Even being defined based on concepts that maps to GDPR regulation, proposed solution and concepts are generic enough to be used by Operators on any country.

The document details aspects regarding the Consent Management, which includes following concepts:

- CRUD operations for a Consent, i.e.: Create, Read, Update or Delete a Consent Record. Specific details will be handled as for example in fact Consents may not be deleted but Revoked instead.
- What information is relevant in the managed Consent record, e.g.: end User Identifier to which the Consent is associated, expiration/validity, etc.
- Policy enforcement to validate existence and validity of Consent before Authorizing access to a (set of) scopes.
- Allow end Users to view and update their Consents.
- Flows detailing how Consent can be collected from the User without degrading the user experience while using a third-party service in an Application.

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
-	`Consent`: an explicit opt-in action that the user takes to allow processing of their Personal Information. It grants a **legal** entity (e.g., the operator or a specific third party) access to a set of **scopes** of a given **user**, under a specific **purpose**.
-	`Legal Entities`: are the legal subjects that are willing to get access to personal information with a specific, predefined purpose.

>[TO BE EDITED/COMPLETED]

## Purposes

A purpose declares what the application intends to do with a set of Personal Information resources. It provides a human-friendly description of why the data processing takes place and a list of the scopes of that Personal Information that gives access to its processing.

Purposes can be grouped into three categories*: 

- **Automatic purpose** : This purpose does NOT require explicit opt-in from the user and cannot be revoked. For example, you need to handle call logs to create an invoice.
- **Opt-out purpose** : This purpose does not require explicit opt-in from the user, but consent can be revoked. For example, processing invoices to improve our commercial offering and for marketing purposes.
- **Opt-in purpose** : This purpose requires explicit user opt-in and can be revoked. For example, creating a credit score for a user based on CDRs and payment history.

_(*)NOTE: Some categories may NOT be allowed for all 3rd party Apps. (i.e. Automatic Purpose)_

The explicit opt-int of the user can be collected either for all their identifiers or only for certain identifiers of the user. Accordingly, there are two types of purposes::

-	**User-bound**: the consent is granted to be applied for all the identifiers of a given user i.e. granted at user level. For example, grant consent for accessing to the call history of any of their phone numbers. 
-	**Identifier-bound**: the consent is granted to be applied only for a specific user’s identifier i.e. for a public identifier associated with the service the Telco Operator provides to the user (typically a phone number). For example, grant consent for accessing to the call history of only one of their phone numbers.

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
  - These are scopes that refer to resources providing access to Personal Information and thus requiring special protection as stated by e.g. the GDPR law.
  - Getting access to PI scopes MUST always be done by explicitly declaring a purpose.
- Purposes are assigned to an application.

Therefore, an application will have access to:
  - APIs or resources providing personal information under the assigned purpose (because of the PI scopes included in that purpose)
    - **Only** if user has granted her consent for that purpose
    - Which may happen automatically (automatic purposes) or may need an explicit user action to provide consent (depending on the purpose level of the purpose associated to that consent). In the second case, the consent is created and stored and will be validated during authorization.

>[TO BE EDITED/COMPLETED]

### Purpose definition

>[TO BE EDITED/COMPLETED] - purpose definition (naming + description) and format. Data Privacy Vocabulary: https://www.w3.org/community/dpvcg/​ | https://w3c.github.io/dpv/dpv/#vocab-purpose

### Applying purpose concenpt in the authorization request

>[TO BE EDITED/COMPLETED - [OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/rfc/rfc6749)]

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
