# CAMARA Security and Interoperability Profile

## Table of Contents

<!-- TOC -->
  * [Introduction](#introduction)
  * [Audience](#audience)
  * [Conventions](#conventions)
  * [General Considerations](#general-considerations)
    * [Transport Security](#transport-security)
    * [Sender-Constrained Tokens](#sender-constrained-tokens)
  * [OIDC Authorization Code Flow](#oidc-authorization-code-flow)
    * [Signed Authentication Requests](#signed-authentication-requests)
    * [Optional Parameters](#optional-parameters)
    * [Cross-Site Request Forgery Protection](#cross-site-request-forgery-protection)
  * [Client-Initiated Backchannel Authentication Flow](#client-initiated-backchannel-authentication-flow)
    * [Optional Parameters](#optional-parameters-1)
    * [Authentication Request](#authentication-request)
  * [Format of `login_hint`](#format-of-login_hint)
  * [Offline Access](#offline-access)
      * [Refresh Token Issuance](#refresh-token-issuance)
    * [Refresh Token Usage](#refresh-token-usage)
    * [Refresh Token Security](#refresh-token-security)
  * [Client Credentials Flow](#client-credentials-flow)
  * [JWT Bearer Flow](#jwt-bearer-flow)
  * [Handling of acr_values](#handling-of-acr_values)
  * [Access Token Request](#access-token-request)
  * [The Scope Parameter](#the-scope-parameter)
  * [Missing "openid" scope](#missing-openid-scope)
  * [Purpose](#purpose)
    * [Purpose as a scope](#purpose-as-a-scope)
    * [Outlook on purpose-handling leveraging Rich Authorization Request](#outlook-on-purpose-handling-leveraging-rich-authorization-request)
  * [ID Token](#id-token)
    * [ID Token sub claim](#id-token-sub-claim)
  * [Client Authentication](#client-authentication)
  * [OpenId Foundation Certification](#openid-foundation-certification)
  * [References](#references)
  * [Appendix A (Normative): Error Responses](#appendix-a-normative-error-responses)
    * [Authentication Error Response](#authentication-error-response)
      * [Authorization Code Flow](#authorization-code-flow)
      * [Client-Initiated Backchannel Authentication Flow](#client-initiated-backchannel-authentication-flow-1)
    * [Token Error Response](#token-error-response)
      * [Authorization Code Flow](#authorization-code-flow-1)
      * [Client-Initiated Backchannel Authentication Flow](#client-initiated-backchannel-authentication-flow-2)
      * [Refresh Token Flow](#refresh-token-flow)
<!-- TOC -->

## Introduction

This document is the CAMARA Security and Interoperability Profile. To ensure interoperability and increased security, this technical specification restricts some options available in OIDC and CIBA, but does not change these standards.

The CAMARA document sharpens the following for interoperability and security: 

* Of the flows defined in OIDC, CAMARA uses the [OIDC Authorization Code Flow](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) and [OIDC Client Initiated Backchannel Authentication Flow](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html)
* Of the flows defined in Oauth2, CAMARA uses the [Client credentials grant flow](https://www.rfc-editor.org/rfc/rfc6749#section-4.4)
* Scope Parameter. Recommendations about the format of the scope parameter used in  CAMARA APIs.

* Client Authentication. Specifications for client authentication within CAMARA.

By encapsulating these elements within this document, it aims to provide a comprehensive guide for API consumers and API providers, ensuring consistent implementation and adherence to standardized security measures across the CAMARA ecosystem. The defined OIDC profile not only facilitates the integration process, but also serves as a basic framework for developers wishing to leverage the CAMARA APIs while maintaining security and interoperability.


## Audience

The target audience for this document is the service/technical departments of API providers exposing network functions via standard CAMARA APIs and the applications or client systems that consume CAMARA APIs to make use of the operator's network capabilities.

## Conventions

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

Unless otherwise noted, all the protocol parameter names and values are case sensitive.

## General Considerations

### Transport Security
All network connections MUST use TLS 1.2 or better.

### Sender-Constrained Tokens

[OAuth 2.0 Security Best Current Practice](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics#section-2.2.1) and the [FAPI 2.0 Baseline Profile](https://openid.net/specs/fapi-2_0-baseline-01.html) both RECOMMEND that authorization and resource servers use mechanisms for sender-constraining access tokens. 

This document states that Demonstrating Proof of Possession (DPoP) [RFC9449](https://datatracker.ietf.org/doc/html/rfc9449) MAY be used by API Consumers, to prevent misuse of stolen and leaked access tokens. 

CAMARA authorization servers MUST NOT respond with an error if they do not support DPoP. DPoP allows authorization servers to issue tokens that are not sender-constrained even if a valid DPoP header is present in the authorization request. It is up to the API consumer to decide whether none-sender-constrained tokens meet their security requirements.

If the API Provider supports DPoP, support for it MAY be expressed by the server metadata field `dpop_signing_alg_values_supported` or alternate API documentation.

API consumers with high security demands that e.g. want to achieve EIDAS LOA high can be set to be required to always send DPoP requests. This requirement is expressed by the API consumer's metadata in the field `dpop_bound_access_tokens`. This requirement on the API consumer is determined at onboarding time.

The following table defines the REQUIRED behaviour of the API Provider for the `/token` endpoint, dependent on whether a DPoP proof is provided, and the API Provider's own level of DPoP support.

| DPoP Proof Provided | API Provider DPoP Support  | Token Type Issued  |
|:-----------------------:|:-------------------------------:|:-------------------:|                                                                               
| Yes                              | No DPoP Support                  | Bearer token        |
| Yes                              | Supports DPoP                      | DPoP token          |
| Yes                              | Requires DPoP                       | DPoP token          |                                                                                       
| No                               | No DPoP Support                 | Bearer token         |
| No                               | Supports DPoP                     | Bearer token         |
| No                               | Requires DPoP                      | HTTP 400 `invalid_dpop_proof`<br>(see RFC [9449](https://www.rfc-editor.org/rfc/rfc9449.html#name-oauth-extensions-error-regi)) |

## OIDC Authorization Code Flow

The OIDC Authorization Code Flow is defined in [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html). CAMARA REQUIRES API Providers to use network-based authentication in the Authorization Code Flow. Therefore, access tokens are issued for the network authenticated identifier.

It is up to the API Provider to make a decision when consent collection is needed or not, based on the scope(s)/purpose declared by the API Consumer and aligned with local legislation, ensuring that all API Providers under the same regulatory framework adopt a consistent approach. If consent is required, the API Provider SHALL obtain it according to [OIDC section 3.1.2.4](https://openid.net/specs/openid-connect-core-1_0.html#Consent). CAMARA does not prevent the API Provider from using additional authentication methods as part of the in-band consent capture process if consent is required.

---
**NOTE**

CAMARA does not intend to limit the Authorization Code Flow to just network-based authentication in the future. New workstreams will be initiated to define the necessary changes that will ensure the Authorization Code Flow can be interoperably used with both network-based and non-network-based authentication methods.

--- 

### Signed Authentication Requests

It is RECOMMENDED that signed authentication requests be used, as specified by [OIDC](https://openid.net/specs/openid-connect-core-1_0.html#JWTRequests). The same key MAY be used for signing the authentication request as is used for [client authentication](#client-authentication). In addition, this document defines that:
  * Values for the mandatory parameters `scope`, `response_type`, `client_id` and `redirect_uri` MUST be included using the OAuth2.0 request syntax. The values for these parameters MUST match those in the signed request object.
  * For all other parameters, the authorization server MUST only use the parameters contained in the signed request object. 

It is RECOMMENDED that the value of the `aud` field of the signed authentication request is the URL of the [Authorization Endpoint](https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint).
The authorization server MAY accept different values of the `aud` field e.g. the `issuer` field of its [OpenID Provider Metadata](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata). 
The authorization server MUST check the value of the `aud` field and reject signed authentication requests if the value of the `aud` is not associated with the authorization server.

Note: Care must be taken in a multi-tenant environment that a signed authentication request for one tenant is not accepted at another tenant endpoint.

Note: The [Security Considerations](https://www.rfc-editor.org/rfc/rfc9101.html#section-10) of RFC9191 apply. It is recommended that the API consumer never includes a `sub` field in the signed request object, because otherwise the signed request object might be used for client authentication. For security reasons the endpoint receiving the request is explicitly named in the `aud` field.

### Optional Parameters

The OIDC Authentication Request defines login_hint as an OPTIONAL request parameter. CAMARA does not define a specific handling for this parameter in OIDC Authorization Code Flow. To ensure better interoperability, CAMARA clarifies that, if login_hint parameter is present in the authentication request and the authorization server does not support it, it MAY ignore it. It is RECOMMENDED that the authorization server does not return an error if the login_hint is not supported.

### Cross-Site Request Forgery Protection

CAMARA REQUIRES cross-site request forgery (CSRF) protection.

CAMARA RECOMMENDS PKCE for CSRF protection. 
CAMARA Authorization Servers SHOULD implement PKCE. If PKCE is not used by the API consumer then the CAMARA authorization server must handle **state** and **nonce** as defined in OAuth2.

CAMARA API consumers SHOULD use PKCE if the CAMARA authorization server supports PKCE.

If nonce for CSRF-protection is used then implementers must ensure that sufficient entropy is present in the nonce value.
Please see [OAuth 2.0 Security Best Current Practice](https://oauthstuff.github.io/draft-ietf-oauth-security-topics/draft-ietf-oauth-security-topics.html#name-protecting-redirect-based-f).

## Client-Initiated Backchannel Authentication Flow

The [Client-Initiated Backchannel Authentication (CIBA)](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html) flow is used to initiate an out-of-band authentication of the end-user.

Communication with the Backchannel Authentication Endpoint MUST utilize TLS.

CIBA allows the Client to get the authentication result in three ways: poll, ping, or push. This profile allows clients to use the *poll* mode. In the Poll mode, the authentication result is retrieved by the Client by polling the token endpoint using the new grant type. 

### Optional Parameters

The parameters `binding_message`, `user_code`, and `requested_expiry` are currently not implemented in CAMARA and for interoperability this document defines that the authorization server SHOULD ignore them. 


### Authentication Request

It is RECOMMENDED that signed authentication requests be used, as specified by [OIDC CIBA Core](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#signed_auth_request). The same key MAY be used for signing the authentication request as is used for [client authentication](#client-authentication).

CIBA allows the client to use login_hint_token, id_token_hint or login_hint as a hint in the authentication request. This CAMARA profile makes the login_hint parameter REQUIRED. The client SHALL specify login_hint (and only login_hint) in the authentication request when using CIBA in a CAMARA context.

The client MUST authenticate with the authorization server as described in [Client Authentication Section](#client-authentication).

## Format of `login_hint`

This CAMARA document clarifies the values used in login_hint in the following way:

  * **_tel_**

    For phone numbers. The `login_hint` must be a tel URI as defined in [RFC 3966](https://www.rfc-editor.org/info/rfc3966) for global phone numbers without visual separators in [E.164](https://www.itu.int/rec/T-REC-E.164-201011-I/en) format. For example, `tel:+34666666666`.

  * **_ipport_**

    For IPv4 and IPv6 addresses, that can optionally include a port. For example, `ipport:80.90.34.2:16790`, `ipport:80.90.34.2`, `ipport:[2001:db8::1]:8080` or `ipport:[2001:db8::1]`.

  * **_operatortoken_**

    For operator tokens as defined by [GSMA TS.43](https://www.gsma.com/newsroom/gsma_resources/ts-43-service-entitlement-configuration/) and [GSMA ASAC](https://www.gsma.com/newsroom/gsma_resources/asac-01-v1-0/). TS.43 does not specify the format of the operator token.
    
   This document does not specifiy how the API consumer got the operatorToken. 

## Offline Access

#### Refresh Token Issuance

Neither OIDC, CIBA, nor OAuth2 define a way for clients to indicate whether they need a refresh_token. 
Refresh token issuance is optional and at the discretion of the authorization server.

CAMARA uses the scope `offline_access` in the authorization request to indicate to the authorization server that the client requests a refresh token additionally to the access token for CAMARA API access.

---
**NOTE**

OIDC defines the scope `offline_access` and it is used to get an access token to be used at the UserInfo endpoint, and also to obtain a request token to refresh this access token.

---

The OIDC rules for `offline_access` as defined in [OIDC Offline Access](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccess) apply. 

The OIDC section on [OIDC Using Refresh Tokens](https://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens) is not changed by this document.

The OIDC security and privacy considerations regarding offline access and refresh tokens apply e.g. on
[Token lifetime](https://openid.net/specs/openid-connect-core-1_0.html#TokenLifetime) and [Offline Access Privacy](https://openid.net/specs/openid-connect-core-1_0.html#OfflineAccessPrivacy).

### Refresh Token Usage

This section applies to OIDC Authorization Code Flow and CIBA. Other flows do not need refresh tokens and MUST not use refresh tokens.

In addition to [OIDC Using Refresh Tokens](https://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens) and [OAuth2 Refreshing an Access Token](https://datatracker.ietf.org/doc/html/rfc6749#section-6) this document defines the following:

     * A new access token MUST not be issued if the user has revoked their consent.
     * A new access token MUST not be issued if the client status regarding the requested API access has changed.

---
**NOTE**

New user consent might be required if e.g. the user revoked their consent and the issued access tokens and refresh token are not valid anymore.
As defined in [error response](https://datatracker.ietf.org/doc/html/rfc6749#section-5.2) the error returned in this case is `invalid_grant`. 

---

### Refresh Token Security

Considering [OAuth2 Refresh Token Protection](https://datatracker.ietf.org/doc/html/draft-ietf-oauth-security-topics#refresh_token_protection) this document RECOMMENDS using `Refresh token rotation`.

## Client Credentials Flow

The [OAuth 2.0 Client Credentials](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4) grant type is used to obtain a 2-legged Access Token that does not represent a user. The grant-type can only be used if agreed between the API consumer and the API provider exposing the API, taking into account the declared purpose for accessing the API (cf. [CAMARA API Specification - Authorization and authentication common guidelines](CAMARA-API-access-and-user-consent.md#camara-api-specification---authorization-and-authentication-common-guidelines)).

## JWT Bearer Flow

The JWT-Bearer Flow uses [JWTs as Authorization Grants](https://datatracker.ietf.org/doc/html/rfc7523#section-2.1) ("urn:ietf:params:oauth:grant-type:jwt-bearer" grant_type for Oauth 2.0) that provide a JWT assertion following the OAuth Assertion Framework: [Using Assertions as Authorization Grants](https://datatracker.ietf.org/doc/html/rfc7521#section-4.1) | [Client Acting on Behalf of a User](https://datatracker.ietf.org/doc/html/rfc7521#section-6.3).

The scope parameter is MUST NOT be specified for JWT-Bearer Flow but the scope claim in the assertion is mandatory. 

The claim of the assertion are.

- The mandatory `iss` claim MUST be the client id of the API Consumer.
- The mandatory `sub` (subject) claim MUST identifies the principal that is the subject of the JWT.
  -  The value MUST be either a phonenumber prefixed by "tel:" or a TS.43 token prefixed by "operatortoken:".
- The mandatory `aud` claim MUST be the URL of the endpoint this token request is sent to.
- The mandatory `exp` claim as defined in [RFC7519 section 4.1.4](https://www.rfc-editor.org/rfc/rfc7519.html#section-4.1.4).
- The mandatory `iat` claim as defined in [RFC7519 section 4.1.6](https://www.rfc-editor.org/rfc/rfc7519.html#section-4.1.6).
- The mandatory `jti` claim as defined in [RFC7519 section 4.1.7](https://www.rfc-editor.org/rfc/rfc7519.html#section-4.1.7).
- The mandatory `scope` claim that MUST contain a [Purpose](#purpose) and CAMARA scope values. 

Client Authentication is optional for JWT-Bearer Flow and is not needed in this CAMARA token request because the assertion is signed by the API Consumer and the assertion is interpreted as the client assertion.

The request SHALL be rejected by the authorisation server if the exp claim is more than 300 seconds later than the time of receipt. Additionally, if the iat claim is present, the request SHALL be rejected if the difference between the exp claim and iat claim is more than 300 seconds.

This flow SHOULD return short-lived access tokens.

This flow MUST NOT return refresh tokens.

## Handling of acr_values

OIDC specifies in [Mandatory to Implement Features for All OpenID Providers](https://openid.net/specs/openid-connect-core-1_0.html#ServerMTI) that OpenId Providers MUST implement support for acr_values.

> Authentication Context Class Reference
>>    OPs MUST support requests for specific Authentication Context Class Reference values via the acr_values parameter, as defined in Section 3.1.2. (Note that the minimum level of support required for this parameter is simply to have its use not result in an error.)

OIDC also defines that the parameter acr_values is OPTIONAL and does not specify possible values. This leads to interoperability issues.

This documents defines that CAMARA OpenId Providers MUST ignore the parameter acr_values. 

This document defines that CAMARA API consumers SHOULD not use the acr_values parameter. 

> To foster interoperability a future version of this document might define values for the acr_values parameter acceptable in CAMARA.

## Access Token Request

This CAMARA document makes the scope parameter REQUIRED for the OAuth2 Client Credentials Grant.

The client MUST authenticate with the authorization server as described in [Client Authentication Section](#client-authentication).

## The Scope Parameter


Scope values determine the specific CAMARA services being requested by the Service Provider, subject to the SP being registered to use those services. The scope values must be documented in the API OAS files by all CAMARA API subprojects. This document does not change OIDC definitions of scope values.


---
**NOTE**

Scope values are an integral part of any OAuth2 and OIDC implementation. The API implementation enforces API access based on scope (if the CAMARA API subproject defines scopes).
Therefore scopes should be available to API implementations.

---


## Missing "openid" scope

[OpenID Connect Core Authentication Request](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) states the following about the value of scope.
> REQUIRED. OpenID Connect requests MUST contain the openid scope value. If the openid scope value is not present, the behavior is entirely unspecified. Other scope values MAY be present. ...

This document defines that the authentication server SHOULD not return an id token if `openid` is missing in the scope parameter.

This document defines the following error handling for a missing "openid" value in scope. 

For OIDC please refer to [OIDC Authentication Error Response](https://openid.net/specs/openid-connect-core-1_0.html#AuthError).
If "openid" is missing in the scope value but a claim that is [standardized in OIDC](https://openid.net/specs/openid-connect-core-1_0.html#Claims) is requested, then the Authorization Server returns an `invalid_request` error code.

For CIBA please refer to [CIBA Authentication Error Response](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#auth_error_response).
If "openid" is missing in the scope value but a claim that is standardized in OIDC, then the Authorization Server returns an HTTP 400 Bad request response with an `invalid_request` error code.

Clients SHOULD follow the OIDC and CIBA standard and SHOULD include `openid` in the list of requested scopes.
The [id token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) contains the `sub` field which is the identifier of the subject of the [OIDC authorization code](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) request respectively the [CIBA authentication request](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#auth_request). 

The CAMARA authorization server creates the `sub` value. Globally unique identifiers, like the MSISDN, should be avoided for privacy reasons.

## Purpose

In CAMARA `purpose` is used to convey to the user for which purpose an API is used. E.g.: Does the client request the user's location because it wants to show relevant tourist information or is the user's location information protecting the user when they withdraw money from an ATM? 

Purpose has always to be reflected and audited when personal information is accessed or managed in certain legislations (e.g. in EU GDPR). To ensure interoperability across different legislations this profile requires that the purpose must always be declared within the supported OIDC or OAuth2 token requests if personal information is accessed or managed.

### Purpose as a scope

Purpose is one of the scope parameter's values. There MUST be exactly one purpose.

The Authorization Server identifies the purpose using the prefix `dpv:`.

This scope MUST have the following format: `dpv:<dpvValue>` where `<dpvValue>` is coming from [W3C DPV purpose definition](https://w3c.github.io/dpv/2.0/dpv/#vocab-purposes)



### Outlook on purpose-handling leveraging Rich Authorization Request

[RFC 9396 OAuth 2.0 Rich Authorization Requests](https://www.rfc-editor.org/rfc/rfc9396) defines a OAuth2 parameter `authorization_details` that is used to carry fine-grained authorization data. The parameter `authorization_details` can be used in all places where OAuth2 allows the `scope` parameter. That means that the parameter `authorization_details` can be used in all places where OIDC and CIBA allow the `scope` parameter.

Further format discussion will happen, e.g. how to handle multiple purposess, before the current purpose handling will be extended by using the RAR format.

## ID Token

CAMARA uses ID Token as defined in [OpenID Connect Core 1.0 incorporating errata set 2](https://openid.net/specs/openid-connect-core-1_0.html#CodeIDToken). ID token must be a part of a successul response parameter as specified in  [Successful Token Response section](https://openid.net/specs/openid-connect-core-1_0.html#TokenResponse).

### ID Token sub claim

This document defines that the sub claim MUST not be a globally unique identifier.

The sub claim MUST not contain Personally Identifiable Information (PII) such as the MSISDN.

If the sub claim is based on PII then it must be hard for an attacker and colluding clients to reverse the sub claim and reveal the PII.

To prevent [correlation](https://openid.net/specs/openid-connect-core-1_0.html#Correlation) OIDC recommends that the sub claim is a [Pairwise Pseudonymous Identifier (PPID)](https://openid.net/specs/openid-connect-core-1_0.html#Terminology).

This document RECOMMENDS that the sub claim is a Pairwise Pseudonymous Identifier (PPID).

OIDC discusses [Pairwise Identifier Algorithms](https://openid.net/specs/openid-connect-core-1_0.html#PairwiseAlg).

This document does not mandate a particular PPID algorithm to be used.


## Client Authentication

The API consumer MUST authenticate with the authorisation server using `private_key_jwt`, as specified in [OIDC Client Authentication](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication). In addition to the required claims, the signed JWT SHOULD also include the `iat` (issued at) claim.

The API consumer MUST NOT create client assertions with a lifetime of more than 300 seconds, calculated as the difference between the `exp` (expires at) claim and the token creation time (which SHALL also be the value of the `iat` claim if present).

The request SHALL be rejected by the authorisation server if the `exp` claim is more than 300 seconds later than the time of receipt. Additionally, if the `iat` claim is present, the request SHALL be rejected if the difference between the `exp` claim and `iat` claim is more than 300 seconds.

This document RECOMMENDS that for [OIDC Authorization Code Flow](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth) and [OAuth2 Client Credentials Grant](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4) the audience SHOULD be the URL of the Authorization Server's [Token Endpoint](https://openid.net/specs/openid-connect-core-1_0.html#TokenEndpoint).
This document RECOMMENDS that for OIDC CIBA the audience SHOULD be the [Backchannel Authentication Endpoint](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#auth_backchannel_endpoint).

## OpenId Foundation Certification

CAMARA recommends that implementations run the OIDF interoperability suite and achieve [OIDF certification](https://openid.net/certification/).

## References

* [Data Privacy Vocabulary (DPV)](https://w3c.github.io/dpv/2.0/dpv/)
* [E.164 - The international public telecommunication numbering plan](https://www.itu.int/rec/T-REC-E.164-201011-I/en)
* [OpenID Connect Core 1.0 specification](https://openid.net/specs/openid-connect-core-1_0.html)
* [OpenID Connect Client-Initiated Backchannel Authentication Flow - Core 1.0](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html)
* [RFC 2617 - HTTP Authentication: Basic and Digest Access Authentication](https://www.rfc-editor.org/info/rfc2617)
* [RFC 3986 - Uniform Resource Identifier (URI): Generic Syntax](https://www.rfc-editor.org/info/rfc3986)
* [RFC 3966 - The tel URI for Telephone Numbers](https://www.rfc-editor.org/info/rfc3966)
* [RFC 6749 - The OAuth 2.0 Authorization Framework](https://www.rfc-editor.org/info/rfc6749)
* [RFC 7515 - JSON Web Signature (JWS)](https://www.rfc-editor.org/info/rfc7515)
* [RFC 7519 - JSON Web Token (JWT)](https://www.rfc-editor.org/info/rfc7519)
* [RFC 7521 - Assertion Framework for OAuth 2.0 Client Authentication and Authorization Grants](https://www.rfc-editor.org/info/rfc7521)
* [RFC 7523 - JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants](https://www.rfc-editor.org/info/rfc7523)
* [RFC 7636 - Proof Key for Code Exchange by OAuth Public Clients](https://www.rfc-editor.org/info/rfc7636)
* [RFC 8259 - The JavaScript Object Notation (JSON) Data Interchange Format](https://www.rfc-editor.org/info/rfc8259)
* [RFC 8414 - OAuth 2.0 Authorization Server Metadata](https://www.rfc-editor.org/info/rfc8414)
* [RFC 9449 - OAuth 2.0 Demonstrating Proof of Possession (DPoP)](https://www.rfc-editor.org/info/rfc9449)
* [GSMA GSMA Authorization Server – Authenticator capabilities](https://www.gsma.com/newsroom/gsma_resources/asac-01-v1-0/)
* [GSMA TS.43 Service Entitlement Configuration](https://www.gsma.com/newsroom/gsma_resources/ts-43-service-entitlement-configuration/)

## Appendix A (Normative): Error Responses

This section describes the error responses that the Authorization Server MUST return for all flows profiled by this document. Based on the supported functionality, the set of errors and the scenarios that can generate them are defined from the specifications already referenced in this document, such as CIBA, OIDC and OAuth 2.0. This appendix does not change the definition of error responses specified in these standards.

### Authentication Error Response

#### Authorization Code Flow

If the request fails due to a missing, invalid, or mismatching redirection URI, or if the client identifier is missing or invalid, the authorization server MUST NOT redirect the user-agent and SHOULD inform the resource owner of the error. For instance, the authorization server MAY display a message to the user describing the problem.

In other cases, as defined in [OIDC Authentication Error Response Section](https://openid.net/specs/openid-connect-core-1_0.html#AuthError), the authorization server redirects the user-agent to the provided client redirection URI using the HTTP status code `302-Found` and includes the following `error` code parameter within the response:


|         Error Code          | Scenario                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
|:---------------------------:|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|      `invalid_request`      | The request is missing a required parameter, includes an invalid parameter value, includes a parameter more than once, or is otherwise malformed.<br/>If the server requires [Proof Key for Code Exchange (PKCE)](https://www.rfc-editor.org/rfc/rfc7636#section-4.4.1) by OAuth public clients and the client does not send the `code_challenge` in the request.<br/>If the server supporting PKCE does not support the requested `code_challenge_method`. |
|    `unauthorized_client`    | The client is not authorized to request an authorization code using this method.                                                                                                                                                                                                                                                                                                                                                                            |
|       `access_denied`       | The user or authorization server denied the request (for example, the user cannot be authenticated or denied the consent).                                                                                                                                                                                                                                                                                                                                  |
| `unsupported_response_type` | The authorization server does not support obtaining an authorization code using this method.                                                                                                                                                                                                                                                                                                                                                                |
|       `invalid_scope`       | The requested scope is either invalid, unknown, or malformed.                                                                                                                                                                                                                                                                                                                                                                                               |
|     `consent_required`      | The authorization server requires user consent. This error MAY be returned when the `prompt` parameter value in the request is `none`, but the request cannot be completed without displaying a user interface for user consent. This error MAY be returned when the `prompt` parameter value in the request is `consent`, but the authorization server cannot obtain consent.                                                                              |
|       `server_error`        | The authorization server encountered an unexpected condition that prevented it from fulfilling the request.                                                                                                                                                                                                                                                                                                                                                 |
|  `temporarily_unavailable`  | The authorization server is currently unable to handle the request due to a temporary overloading or maintenance of the server.                                                                                                                                                                                                                                                                                                                             |
|   `request_not_supported`   | The authorization server does not support use of the `request` parameter.                                                                                                                                                                                                                                                                                                                                                                                   |
| `request_uri_not_supported` | The authorization server does not support use of the `request_uri` parameter.                                                                                                                                                                                                                                                                                                                                                                               |


#### Client-Initiated Backchannel Authentication Flow

As described in [CIBA Authentication Error Response Section](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.13), an Authentication Error Response is returned directly from the Backchannel Authentication Endpoint in response to the Authentication Request sent by the Client. The authorization server responds with a status code and includes the following `error` code attribute within the response:

<table>
  <thead>
    <tr>
      <th>Status code</th>
      <th>Error code</th>
      <th>Scenario</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="5">400 - Bad Request</td>
      <td><code>invalid_request</code></td>
      <td>The request is either missing a required parameter, includes an invalid parameter value, includes a parameter more than once, or is otherwise malformed.</td>
    </tr>
    <tr>
      <td><code>unauthorized_client</code></td>
      <td>The authenticated application is not authorized to use this authorization grant type.</td>
    </tr>
    <tr>
      <td><code>unknown_user_id</code></td>
      <td>Unable to identify which end-user the application wishes to be authenticated by means of the <code>login_hint</code> provided in the request.</td>
    </tr>
    <tr>
      <td><code>invalid_scope</code></td>
      <td>The requested scope is either invalid, unknown, or malformed.</td>
    </tr>
    <tr>
      <td><code>request_not_supported</code></td>
      <td>The authorization server does not support use of the <code>request</code> parameter.</td>
    </tr>
    <tr>
      <td>401 - Unauthorized</td>
      <td><code>invalid_client</code></td>
      <td>Application authentication failed (as in unknown client, no client authentication included, unsupported authentication method, or <code>client_assertion</code> JWT is not valid).</td>
    </tr>
    <tr>
      <td>403 - Forbidden</td>  
      <td><code>access_denied</code></td>
      <td>The user or the authorization server denied the request. Note that as the authentication error response is received prior to any user interaction, such an error would only be received if a user or the Authserver had made a decision to deny a certain type of request or requests from a certain type of client.</td>
    </tr>
  </tbody>
</table>

### Token Error Response

As defined in [OAuth 2.0 Token Error Response Section](https://www.rfc-editor.org/rfc/rfc6749.html#section-5.2), a Token Error Response is returned directly from the Token Endpoint in response to the Token Request sent by the Client. The authorization server responds with a status code and includes the following `error` code attribute within the response:

<table>
  <thead>
    <tr>
      <th>Status Code</th>
      <th>Error Code</th>
      <th>Scenario</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="4">400 - Bad Request</td>
      <td><code>invalid_request</code></td>
      <td>The request is either missing a required parameter, includes an invalid parameter value, includes a parameter more than once, or is otherwise malformed. In CIBA, if an application receives an <code>invalid_request</code> error it must not make any further requests for the same <code>auth_req_id</code>.</td>
    </tr>
    <tr>
      <td><code>unauthorized_client</code></td>
      <td>The authenticated client is not authorized to use this authorization grant type.</td>
    </tr>
    <tr>
      <td><code>unsupported_grant_type</code></td>
      <td>The authorization grant type is not supported by the authorization server.</td>
    </tr>
    <tr>
      <td><code>invalid_scope</code></td>
      <td>The requested scope is invalid, unknown, malformed, or exceeds the scope granted by the user.</td>
    </tr>
    <tr>
      <td>401 - Unauthorized</td>
      <td><code>invalid_client</code></td>
      <td>Client authentication failed (as in unknown client, no client authentication included, unsupported authentication method, or <code>client_assertion</code> JWT is not valid).</td>
    </tr>
  </tbody>
</table>

#### Authorization Code Flow

In addition to the error codes defined in the common [Token Error Response Section](#token-error-response), the following error codes  and scenarios are specific to the Authorization Code flow:

| Status Code       | Error Code      | Scenario                                                                                                                                                                                                                                                                                                                                            |
|-------------------|-----------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 400 - Bad Request | `invalid_grant` | The provided authorization code is invalid, expired, revoked (for instance, there is no consent from the user), does not match the redirection URI used in the authorization request, or was issued to another client. <br/>If the server requires PKCE, the `code_verifier` does not match the `code_challenge` used in the authorization request. |

#### Client-Initiated Backchannel Authentication Flow

As described in [CIBA Token Error Response Section](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.11), in addition to the error codes defined in the common [Token Error Response Section](#token-error-response), the following error codes  and scenarios are specific to the CIBA flow:

<table>
  <thead>
    <tr>
      <th>Status Code</th>
      <th>Error Code</th>
      <th>Scenario</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td rowspan="5">400 - Bad Request</td>
      <td><code>access_denied</code></td>
      <td>The user denied the authorization request (for example, there is no consent from the user).</td>
    </tr>
    <tr>
      <td><code>invalid_grant</code></td>
      <td>The provided <code>auth_req_id</code> is invalid, revoked or was issued to another client.</td>
    </tr>
    <tr>
      <td><code>authorization_pending</code></td>
      <td>The authorization request is still pending as the user hasn't yet been authenticated or hasn't authorized the request.</td>
    </tr>
    <tr>
      <td><code>slow_down</code></td>
      <td>A variant of <code>authorization_pending</code>, the authorization request is still pending and polling should continue, but the interval MUST be increased by at least 5 seconds for this and all subsequent requests.</td>
    </tr>
    <tr>
      <td><code>expired_token</code></td>
      <td>The <code>auth_req_id</code> has expired. The client will need to make a new Authentication Request.</td>
    </tr>
  </tbody>
</table>

#### Refresh Token Flow

In addition to the error codes defined in the common [Token Error Response Section](#token-error-response), the following error codes and scenarios are specific to the Refresh Token flow:

| Status Code       | Error Code      | Scenario                                                                                                                                    |
|-------------------|-----------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| 400 - Bad Request | `invalid_grant` | The provided refresh token is invalid, expired, revoked (for instance, there is no consent from the user), or was issued to another client. |



