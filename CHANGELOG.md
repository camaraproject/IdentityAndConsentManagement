# Changelog Identity And Consent Management

## Table of Contents

- [r0.2.1](#r021)
- [r0.2.0](#r020)
- [r0.2.0-rc.2](#r020-rc2)
- [v0.2.0-rc.1](#v020-rc1)
- [v0.1.0 - Initial version](#v010---initial-version)

# r0.2.1

**This is the public release of "Identity And Consent Management" version 0.2.1, a patch release from r0.2.0**

## Please note:

* The r0.2.1 release is a patch release of r0.2.0. **Please read also the release notes and changes of [r0.2.0](#r020)**.

### Fixed
* Fixed broken W3C Data Privacy Vocabulary (DPV) reference links in ICM documentation by @jpengar in https://github.com/camaraproject/IdentityAndConsentManagement/pull/196

**Full Changelog**: https://github.com/camaraproject/IdentityAndConsentManagement/compare/r0.2.0...r0.2.1

# r0.2.0

**This is the public release of "Identity And Consent Management" version 0.2.0**

## Please note:

* The content of the release includes the "Identity And Consent Management" approved deliverables in **[documentation](https://github.com/camaraproject/IdentityAndConsentManagement/tree/r0.2.0/documentation)** folder.
* The document [Authentication and Authorization Concept for Service APIs](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-AuthN-AuthZ-Concept.md) was part of the 0.1.0 release. **It has been deprecated** and been removed in the course of the public release of "Identity and Consent Management" version 0.2.0.

### Main Changes
* Creation of the [CAMARA Security and Interoperability Profile](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-Security-Interoperability.md) document.
* Creation of the [Identity and Consent Management Examples](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-ICM-examples.md) document.
* Aligment of the [CAMARA APIs access and user consent management](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-API-access-and-user-consent.md) document with the latest decisions of the working group in the new profile.

### Added
* Added paragraph describing the handling on authorization flow selection during API product ordering in the [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-API-access-and-user-consent.md) document by @Elisabeth-Ericsson in https://github.com/camaraproject/IdentityAndConsentManagement/pull/120
* Added the [`CAMARA-Security-Interoperability.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-Security-Interoperability.md) profile document by @AxelNennker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/121
* Added the [`CAMARA-ICM-examples.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-ICM-examples.md) document by @AxelNennker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/148
* Added 2-legged/3-legged access token definition to [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-API-access-and-user-consent.md) document by @jpengar in https://github.com/camaraproject/IdentityAndConsentManagement/pull/162
  
### Changed
* Clarified resource server terminology by @Elisabeth-Ericsson in https://github.com/camaraproject/IdentityAndConsentManagement/pull/135
* Updated the [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-API-access-and-user-consent.md) document with the latest decisions of the working group in the new profile by @jpengar in https://github.com/camaraproject/IdentityAndConsentManagement/pull/155
* Adapted the `info.description` template in [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-API-access-and-user-consent.md) document to "CAMARA Security and Interoperability Profile" by @AxelNennker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/168
* Editorial changes to CAMARA-API-access-and-user-consent.md by @AxelNennker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/183
* Replaced link into main branch of Identity and Consent Management within "Authorization and authentication" text template (need to be applied to all APIs who have copied the template previously) by @hdamker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/186
* Replace internal links between icm documents with relative links by @hdamker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/188

### Fixed
* Fixed Auth code flow error scenario when user refuses consent in [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0/documentation/CAMARA-API-access-and-user-consent.md) document by @jpengar in https://github.com/camaraproject/IdentityAndConsentManagement/pull/170.
* Fix returned error in authentication code flow by @garciasolero in https://github.com/camaraproject/IdentityAndConsentManagement/pull/180
* Fixed the release tag according to Release Management Working Group guidelines.

### Removed
* Removed deprecated [`CAMARA-AuthN-AuthZ-Concept.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/r0.2.0-rc.2/documentation/CAMARA-AuthN-AuthZ-Concept.md) document by @jpengar in https://github.com/camaraproject/IdentityAndConsentManagement/pull/192

## New Contributors
* @Elisabeth-Ericsson made their first contribution in https://github.com/camaraproject/IdentityAndConsentManagement/pull/120
* @AxelNennker made their first contribution in https://github.com/camaraproject/IdentityAndConsentManagement/pull/121
* @hdamker made their first contribution in https://github.com/camaraproject/IdentityAndConsentManagement/pull/147
* @garciasolero made their first contribution in https://github.com/camaraproject/IdentityAndConsentManagement/pull/180

**Full Changelog**: https://github.com/camaraproject/IdentityAndConsentManagement/compare/v0.1.0...r0.2.0

# r0.2.0-rc.2

**This is the second release candidate version for "Identity And Consent Management" release 0.2.0**

## Please note:

* This is a bugfix release.

### Main Changes
* Replaced link into main branch of Identity and Consent Management within "Authorization and authentication" text template (need to be applied to all APIs who have copied the template previously)

### Added
* N/A
  
### Changed
* Editorial changes to CAMARA-API-access-and-user-consent.md by @AxelNennker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/183
* Replaced link into main branch of Identity and Consent Management within "Authorization and authentication" text template (need to be applied to all APIs who have copied the template previously) by @hdamker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/186
* Replace internal links between icm documents with relative links by @hdamker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/188

### Fixed
* Fix returned error in authentication code flow by @garciasolero in https://github.com/camaraproject/IdentityAndConsentManagement/pull/180
* Fixed the release tag according to Release Management Working Group guidelines.

### Removed
* N/A

## New Contributors
* @garciasolero made their first contribution in https://github.com/camaraproject/IdentityAndConsentManagement/pull/180

**Full Changelog**: https://github.com/camaraproject/IdentityAndConsentManagement/compare/v0.2.0-rc.1...r0.2.0-rc.2

# v0.2.0-rc.1

**This is the release candidate version for "Identity And Consent Management" release 0.2.0**

## Please note:

* The working group agreed to proceed directly with a **Release Candidate** version, bypassing the **ALPHA** release, given the stability and closed scope of the 0.2.0 release.
* The content of the release includes the "Identity And Consent Management" approved deliverables in **[documentation](https://github.com/camaraproject/IdentityAndConsentManagement/tree/v0.2.0-rc.1/documentation)** folder.
* The document [Authentication and Authorization Concept for Service APIs](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-AuthN-AuthZ-Concept.md) is part of the 0.1.0 release. **It is deprecated**. It will be removed after the 0.2.0 public release of "Identity and Consent Management".

### Main Changes
* Creation of the [CAMARA Security and Interoperability Profile](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-Security-Interoperability.md) document.
* Creation of the [Identity and Consent Management Examples](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-ICM-examples.md) document.
* Aligment of the [CAMARA APIs access and user consent management](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-API-access-and-user-consent.md) document with the latest decisions of the working group in the new profile.

### Added
* Added paragraph describing the handling on authorization flow selection during API product ordering in the [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-API-access-and-user-consent.md) document by @Elisabeth-Ericsson in https://github.com/camaraproject/IdentityAndConsentManagement/pull/120
* Added the [`CAMARA-Security-Interoperability.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-Security-Interoperability.md) profile document by @AxelNennker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/121
* Added the [`CAMARA-ICM-examples.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-ICM-examples.md) document by @AxelNennker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/148
* Added 2-legged/3-legged access token definition to [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-API-access-and-user-consent.md) document by @jpengar in https://github.com/camaraproject/IdentityAndConsentManagement/pull/162
  
### Changed
* Clarified resource server terminology by @Elisabeth-Ericsson in https://github.com/camaraproject/IdentityAndConsentManagement/pull/135
* Updated the [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-API-access-and-user-consent.md) document with the latest decisions of the working group in the new profile by @jpengar in https://github.com/camaraproject/IdentityAndConsentManagement/pull/155
* Adapted the `info.description` template in [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-API-access-and-user-consent.md) document to "CAMARA Security and Interoperability Profile" by @AxelNennker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/168

### Fixed
* Restored [`CAMARA-AuthN-AuthZ-Concept.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-AuthN-AuthZ-Concept.md) document with deprecation disclaimer by @hdamker in https://github.com/camaraproject/IdentityAndConsentManagement/pull/147
* Fixed Auth code flow error scenario when user refuses consent in [`CAMARA-API-access-and-user-consent.md`](https://github.com/camaraproject/IdentityAndConsentManagement/blob/v0.2.0-rc.1/documentation/CAMARA-API-access-and-user-consent.md) document by @jpengar in https://github.com/camaraproject/IdentityAndConsentManagement/pull/170.

### Removed
* N/A

## New Contributors
* @Elisabeth-Ericsson made their first contribution in https://github.com/camaraproject/IdentityAndConsentManagement/pull/120
* @AxelNennker made their first contribution in https://github.com/camaraproject/IdentityAndConsentManagement/pull/121
* @hdamker made their first contribution in https://github.com/camaraproject/IdentityAndConsentManagement/pull/147

**Full Changelog**: https://github.com/camaraproject/IdentityAndConsentManagement/compare/v0.1.0...v0.2.0-rc.1

# v0.1.0 - Initial version

**Initial version of guidelines and documentation that represents the short-term solution agreed in this working group for accessing CAMARA APIs, to be used as a baseline.**

## Please note:

The content of the release includes:

* Identity And Consent Management approved deliverables in **documentation** folder:
  
  - **[CAMARA-API-access-and-user-consent.md](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/IdentityAndConsentManagement)** - This document defines guidelines for telco operator API exposure platforms to manage CAMARA API access and user consent (when applicable). **This document is the result of the agreements reached in this working group and sets the stage for future discussions**. The major contents of the document added for this baseline release are:
    - [Glossary of Terms and Concepts](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-API-access-and-user-consent.md#glossary-of-terms-and-concepts)
    - [Applying purpose concept in the authorization request](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-API-access-and-user-consent.md#applying-purpose-concept-in-the-authorization-request)
    - [Authorization flows / grant types](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-API-access-and-user-consent.md#authorization-flows--grant-types)
      - [Authorization code flow (Frontend flow)](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-API-access-and-user-consent.md#authorization-code-flow-frontend-flow)
      - [CIBA flow (Backend flow)](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-API-access-and-user-consent.md#ciba-flow-backend-flow)
      - [Client Credentials](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-API-access-and-user-consent.md#client-credentials)
    - [CAMARA API Specification - Authorization and authentication common guidelines](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-API-access-and-user-consent.md#camara-api-specification---authorization-and-authentication-common-guidelines)
  - **[CAMARA-AuthN-AuthZ-Concept.md](https://github.com/camaraproject/IdentityAndConsentManagement/blob/release-0.1.0/documentation/CAMARA-AuthN-AuthZ-Concept.md)** - This document provides general information and generic recommendations about the authentication and authorization concept based on CAMARA scenarios. It is inherited from the original documentation of the [Commonalities WG](https://github.com/camaraproject/WorkingGroups/tree/main/Commonalities). It was moved to this repository in July 2023, when the Commonalities discussions started to take place in a separate Github repository: https://github.com/camaraproject/Commonalities.

**Full Changelog**: https://github.com/camaraproject/IdentityAndConsentManagement/commits/v0.1.0
