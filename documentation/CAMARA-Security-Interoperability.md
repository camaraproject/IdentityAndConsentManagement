# CAMARA Security and Interoperability Profile

## Table of Contents

   * [Introduction](#introduction)
   * [Audience](#audience)
   * [Conventions](#conventions)
   * [General Considerations](#general-considerations)
      + [Transport Security](#transport-security)
   * [OIDC Authorization Code Flow](#oidc-authorization-code-flow)
      + [Cross-Site Request Forgery Protection](#cross-site-request-forgery-protection)
   * [Client-Initiated Backchannel Authentication Flow](#client-initiated-backchannel-authentication-flow)
      + [Optional Parameters](#optional-parameters)
      + [Authentication Request](#authentication-request)
   * [Format of `login_hint`](#format-of-login_hint)
   * [Offline Access](#offline-access)
         - [Refresh Token Issuance](#refresh-token-issuance)
      + [Refresh Token Usage](#refresh-token-usage)
      + [Refresh Token Security](#refresh-token-security)
   * [Client Credentials Flow](#client-credentials-flow)
   * [Handling of acr_values](#handling-of-acr_values)
   * [Access Token Request](#access-token-request)
   * [The Scope Parameter](#the-scope-parameter)
   * [Missing "openid" scope](#missing-openid-scope)
   * [Purpose](#purpose)
   * [ID Token](#id-token)
      + [ID Token sub claim](#id-token-sub-claim)
   * [Client Authentication](#client-authentication)
   * [OpenId Foundation Certification](#openid-foundation-certification)
   * [References](#references)

<!-- TOC end -->


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

## OIDC Authorization Code Flow

The OIDC Authorization Code Flow is defined in [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html)

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

    For operator tokens as defined by [GSMA TS.43](https://www.gsma.com/newsroom/gsma_resources/ts-43-service-entitlement-configuration/) and [GSMA ASAC](https://www.gsma.com/newsroom/gsma_resources/asac-01-v1-0/).
    TS.43 does not specify the format of the operator token and it therefore might contain characters that are not url-safe.
    The API consumer MUST encode the operator token using [base64url](https://www.rfc-editor.org/rfc/rfc7515.html#appendix-C) encoding to make it URL safe.
    For example, `operatortoken:ZXhhbXBsZQ`
    
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

## Handling of acr_values

OIDC specifies in [Mandatory to Implement Features for All OpenID Providers](https://openid.net/specs/openid-connect-core-1_0.html#ServerMTI) that OpenId Providers MUST implement support for acr_values.

> Authentication Context Class Reference
>>    OPs MUST support requests for specific Authentication Context Class Reference values via the acr_values parameter, as defined in Section 3.1.2. (Note that the minimum level of support required for this parameter is simply to have its use not result in an error.)

OIDC also defines that the parameter acr_values is OPTIONAL and does not specify possible values. This leads to interoperability issues.

This documents defines that Camara OpenId Providers MUST ignore the parameter acr_values with a value different from "https://camaraproject.org/acr/networkbasedauthentication".
If the [OIDC Authentication Request](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) has the `acr_values` parameter with the value "https://camaraproject.org/acr/networkbasedauthentication" then the authorization server MUST try to identify the subscriber through network-based authentication. The [ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) returned from the token endpoint SHALL then contain an Authentication Methods References field `amr` which MUST contain the value "https://camaraproject.org/acr/networkbasedauthentication". 
If the OIDC authentication request is not made over a connection where the API provider can do network-based authentication, then the error code of the [Authentication Error Response](https://openid.net/specs/openid-connect-core-1_0.html#AuthError) is REQUIRED to be `400`. The error_description SHOULD be "network-based authentication was not possible".

This document defines that Camara Clients SHOULD not send acr_values parameters with values different from `https://camaraproject.org/acr/networkbasedauthentication`. 

> To foster interoperability a future version of this document might define other values for the acr_values parameter acceptable in Camara.

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

This CAMARA document allows **one** client authentication method, `private_key_jwt`, as defined in OIDC
[OIDC Client Authentication](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)

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
* [RFC 8259 - The JavaScript Object Notation (JSON) Data Interchange Format](https://www.rfc-editor.org/info/rfc8259)
* [RFC 8414 - OAuth 2.0 Authorization Server Metadata](https://www.rfc-editor.org/info/rfc8414)
* [GSMA GSMA Authorization Server – Authenticator capabilities](https://www.gsma.com/newsroom/gsma_resources/asac-01-v1-0/)
* [GSMA TS.43 Service Entitlement Configuration](https://www.gsma.com/newsroom/gsma_resources/ts-43-service-entitlement-configuration/)

