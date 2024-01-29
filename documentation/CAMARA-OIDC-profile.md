# CAMARA OIDC Profile

## Table of Contents
<!-- TOC -->
* [Introduction](#introduction)
* [Audience](#audience)
* [Conventions](#conventions)
* [Authorization using the Authorization Code Flow](#authorization-using-the-authorization-code-flow)
    * [Authentication Request](#authentication-request)
    * [Authentication Response](#authentication-response)
        * [Successful Response](#successful-response)
        * [Error Response](#error-response)
    * [Access Token Request](#access-token-request)
    * [Access Token Response](#access-token-response)
        * [Successful Response](#successful-response-1)
        * [Error Response](#error-response-1)
* [Authorization using the OpenID Connect Client-Initiated Backchannel Authentication Flow (CIBA)](#authorization-using-the-openid-connect-client-initiated-backchannel-authentication-flow-ciba)
    * [Authentication Request](#authentication-request-1)
    * [Authentication Response](#authentication-response-1)
        * [Successful Response](#successful-response-2)
        * [Error Response](#error-response-2)
    * [Access Token Request](#access-token-request-1)
    * [Access Token Response](#access-token-response-1)
        * [Successful Response](#successful-response-3)
        * [Error Response](#error-response-3)
* [Client Credentials Flow](#client-credentials-flow)
    * [Access Token Request](#access-token-request-2)
    * [Access Token Response](#access-token-response-2)
        * [Successful Response](#successful-response-4)
        * [Error Response](#error-response-4)
* [The Scope Parameter](#the-scope-parameter)
* [ID Token](#id-token)
* [Client Authentication](#client-authentication)
* [References](#references)
* [Appendix A. RSA Key Used in Examples](#appendix-a-rsa-key-used-in-examples)
    * [Appendix A.1. Authorization Server RSA Key](#appendix-a1-authorization-server-rsa-key)
    * [Appendix A.2. Client RSA Key](#appendix-a2-client-rsa-key)
<!-- TOC -->


## Introduction

Telco network capabilities exposed through APIs provide great value to customers. By simplifying the complexity of telco networks with APIs and making the APIs available across telco networks and countries, CAMARA enables easy and seamless access. To ensure seamless interoperability and increased security, this technical document introduces the OpenID Connect (OIDC) profile, which is specifically tailored for CAMARA APIs.

OpenID Connect is a widely adopted standard for user authentication and authorization in the context of APIs. This document builds on the basic principles of OIDC and outlines CAMARA-specific guidelines to address CAMARA APIs scenarios and use cases.

The CAMARA OIDC profile includes three different authorization mechanisms: Authorization Code Flow, Client Initiated Backchannel Authentication (CIBA), and Client Credentials. Each method is detailed, providing comprehensive insight into the authorization and token request processes, mandatory parameters, successful response content or potential error scenarios.

Key areas covered include:

* Authorization flows:

    * Authorization Code Flow.
    * Client Initiated Back Channel Authentication (CIBA).
    * Client Credentials.

* Scope Parameter. Definition of scope parameter format applicable to CAMARA APIs.

* ID Token Content. CAMARA specific details about the content of the Id token.

* Client Authentication. Specifications for client authentication within the CAMARA OIDC profile.

By encapsulating these elements within the CAMARA OIDC profile, this document aims to provide a comprehensive guide for developers and operators, ensuring consistent implementation and adherence to standardized security measures across the CAMARA ecosystem. The defined OIDC profile not only facilitates the integration process, but also serves as a basic framework for developers wishing to leverage the CAMARA APIs while maintaining security and interoperability.


## Audience

The target audience for this document is the service/technical departments of operators exposing network functions via standard CAMARA APIs. And applications or client systems that consume CAMARA standard APIs to make use of the operator's network capabilities.


## Conventions

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.


## Authorization using the Authorization Code Flow

The [OAuth 2.0 Authorization Code](https://datatracker.ietf.org/doc/html/rfc6749#section-4.1) flow involves exchanging an authorization code for a token. Instead of requesting authorization directly from the resource owner, the client directs the resource owner to an authorization server (via its user-agent), which in turn directs the resource owner back to the client with the authorization code.

### Authentication Request

An Authentication Request is an [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) Authorization Request that requests that the End-User be authenticated by the Authorization Server.

The CAMARA profile uses the following OpenID Connect request parameters with the Authorization Code Flow:

* **scope**

  REQUIRED. A space delimited and a case-sensitive list of ASCII strings for OAuth 2.0 scope values. OpenID Connect requests MUST contain the `openid` scope value. If the `openid` scope value is not present, the ID Token will not be returned. Other scope values MAY be present. Scope values used that are not understood by an implementation SHOULD be ignored. More information about scope values as described in [The Scope Parameter Section](#the-scope-parameter).


* **response_type**

  REQUIRED. OAuth 2.0 Response Type value that determines the authorization processing flow to be used, including what parameters are returned from the endpoints used. When using the Authorization Code Flow, this value is `code`.


* **client_id**

  REQUIRED. OAuth 2.0 Client Identifier valid at the Authorization Server.


* **redirect_uri**

  REQUIRED. Redirection URI to which the response will be sent. This URI MUST exactly match one of the Redirection URI values for the Client pre-registered at the OpenID Provider, with the matching performed as described in [RFC 3986](https://www.rfc-editor.org/rfc/rfc3986.html#section-6.2.1). When using this flow, the Redirection URI SHOULD use the https scheme; however, it MAY use the http scheme. The Redirection URI MAY use an alternate scheme, such as one that is intended to identify a callback into a native application.


* **state**

  RECOMMENDED. Opaque value used to maintain state between the request and the callback. Typically, Cross-Site Request Forgery (CSRF, XSRF) mitigation is done by cryptographically binding the value of this parameter with a browser cookie.


* **nonce**

  RECOMMENDED. String value used to associate a Client session with an ID Token, and to mitigate replay attacks. The value is passed through unmodified from the Authentication Request to the ID Token. Sufficient entropy MUST be present in the nonce values used to prevent attackers from guessing values. For implementation notes, see [OpenID Connect Core 1.0 specification](https://openid.net/specs/openid-connect-core-1_0.html#NonceNotes).

* **prompt**

  OPTIONAL. Space-delimited, case-sensitive list of ASCII string values that specifies whether the Authorization Server prompts the End-User for reauthentication and consent. The defined values are:

    * **_none_**

      The Authorization Server MUST NOT display any authentication or consent user interface pages. An error is returned if an End-User is not already authenticated or the Client does not have pre-configured consent for the requested Claims or does not fulfill other conditions for processing the request. The error code will typically be `login_required`, `interaction_required`, or another code defined in [Authentication Error Response Section](#error-response). This can be used as a method to check for existing authentication and/or consent.

    * **_login_**

      The Authorization Server SHOULD prompt the End-User for reauthentication. If it cannot reauthenticate the End-User, it MUST return an error, typically `login_required`.

  The `prompt` parameter can be used by the Client to make sure that the End-User is still present for the current session or to bring attention to the request. If this parameter contains `none` with any other value, an error is returned.

  If an Authorization Server receives a `prompt` value outside the set defined above that it does not understand, it SHOULD ignore it; in practice, not returning errors for not-understood values will help facilitate phasing in extensions using new prompt values.


For example, the client directs the user-agent to make the following HTTP request (with line wraps within values for display purposes only):

```http
GET /authorize?
response_type=code&
client_id=s6BhdRkqt3&
state=af0ifjsldkj&
nonce=n-0S6_WzA2Mj&
redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb&
scope=openid%20example-scope HTTP/1.1
Host: server.example.com
```

### Authentication Response


#### Successful Response

If the resource owner grants the access request, the authorization server issues an authorization code and delivers it to the client by adding the following parameters to the query component of the redirection URI using the `application/x-www-form-urlencoded `format:

* **code**

  REQUIRED. The authorization code generated by the authorization server. The authorization code MUST expire shortly after it is issued to mitigate the risk of leaks. A maximum authorization code lifetime of 10 minutes is RECOMMENDED. The client MUST NOT use the authorization code more than once. If an authorization code is used more than once, the authorization server MUST deny the request and SHOULD revoke (when possible) all tokens previously issued based on that authorization code. The authorization code is bound to the client identifier and redirection URI.


* **state**

  REQUIRED if the `state` parameter was present in the client authorization request. The exact value received from the client.


The client MUST ignore unrecognized response parameters.

For example, the authorization server redirects the user-agent by sending the following HTTP response (with line wraps within values for display purposes only):
```http
HTTP/1.1 302 Found 
Location: https://client.example.com/cb?
code=SplxlOBeZQQYbYS6WxSbIA&
state=af0ifjsldkj 
```


#### Error Response

If the request fails due to a missing, invalid, or mismatching redirection URI, or if the client identifier is missing or invalid, the authorization server SHOULD inform the resource owner of the error and MUST NOT automatically redirect the user-agent to the invalid redirection URI.

If the resource owner denies the access request or if the request fails for reasons other than a missing or invalid redirection URI, the authorization server informs the client by adding the following parameters to the query component of the redirection URI using the `application/x-www-form-urlencoded` format:

* **error**

  REQUIRED. A single ASCII error code from the following:

    * **_invalid_request_**

      The request is missing a required parameter, includes an invalid parameter value, includes a parameter more than once, or is otherwise malformed.

    * **_unauthorized_client_**

      The client is not authorized to request an authorization code using this method.

    * **_access_denied_**

      The authorization server denied the request (e.g., the authorization server cannot identify the user).

    * **_unsupported_response_type_**

      The authorization server does not support obtaining an authorization code using this method.

    * **_invalid_scope_**

      The requested scope is invalid, unknown, or malformed.

    * **_server_error_**

      The authorization server encountered an unexpected condition that prevented it from fulfilling the request.

    * **_temporarily_unavailable_**

      The authorization server is currently unable to handle request due to a temporary overloading or maintenance of the server.

    * **_interaction_required_**

      The Authorization Server requires End-User interaction of some form to proceed. This error MAY be returned when the `prompt` parameter value in the Authentication Request is `none`, but the Authentication Request cannot be completed without displaying a user interface for End-User interaction.

    * **_login_required_**

      The Authorization Server requires End-User authentication. This error MAY be returned when the `prompt` parameter value in the Authentication Request is `none`, but the Authentication Request cannot be completed without displaying a user interface for End-User authentication.

    * **_account_selection_required_**

      The End-User is REQUIRED to select a session at the Authorization Server. The End-User MAY be authenticated at the Authorization Server with different associated accounts, but the End-User did not select a session. This error MAY be returned when the `prompt` parameter value in the Authentication Request is `none`, but the Authentication Request cannot be completed without displaying a user interface to prompt for a session to use.

    * **_consent_required_**

      The Authorization Server requires End-User consent. This error MAY be returned when the `prompt` parameter value in the Authentication Request is `none`, but the Authentication Request cannot be completed without displaying a user interface for End-User consent.


* **error_description**

  OPTIONAL. Human-readable ASCII text providing additional information, used to assist the client developer in understanding the error that occurred. Values for the `error_description` parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.


* **state**

  REQUIRED if the `state` parameter was present in the client authorization request. The exact value received from the client.


`error_description` parameter is not a valid field to take decisions on the developer side, as its value may vary depending on the server implementation, language, format or context. Therefore, the developer should base their logic only on the `error` parameter. Moreover, `error_description` message is not intended to be displayed to the user, but only to help the developer debug the issue. If the developer needs to provide a custom error message to the user, they should use their own logic to map the error code to a suitable message, taking into account the user’s language, tone and level of detail.


For example, the authorization server redirects the user-agent by sending the following HTTP response (with line wraps within values for display purposes only):

```http
HTTP/1.1 302 Found
Location: Location: https://client.example.com/cb?
    error=unsupported_response_type&
    error_description=Unsupported%20response_type%20value&
    state=af0ifjsldkj
```


### Access Token Request

The client makes a request to the token endpoint, using the HTTP POST method, by sending the following parameters using the application/x-www-form-urlencoded format with a character encoding of UTF-8 in the HTTP request entity-body:

* **grant_type**

  REQUIRED. Value MUST be set to `authorization_code`.


* **code**

  REQUIRED. The authorization code received from the authorization server.


* **redirect_uri**

  REQUIRED, if the `redirect_uri` parameter was included in the authorization request, and their values MUST be identical.


The client MUST authenticate with the authorization server as described in the [Client Authentication Section](#client-authentication).

For example, the client makes the following HTTP request (with line wraps within values for display purposes only):

```http
POST /token HTTP/1.1 
Host: server.example.com 
Content-Type: application/x-www-form-urlencoded 

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA&
redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb&
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&
client_assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzNkJoZFJrcXQzIiwiYXVkIjoiaHR0cHM6Ly9zZXJ2ZXIuZXhhbXBsZS5jb20iLCJzdWIiOiJzNkJoZFJrcXQzIiwiZXhwIjoxMzAwODE5MzgwLCJpYXQiOjEzMDA4MTkwODAsImp0aSI6ImUyMjA5MTNmLTM5ZjItNDEwNi05ZmU0LWI3YmEzNGJkZjJkNSJ9.khzDX5keZQ6EJiJd8upzUMvAZtH8-PypGrWJYeA-Upx1MRDEr54U0V3T2-XZ93PbQE0waMyn25Z-xiDsCMVazYvOEa_Hz5_mE2x1aVZ8m86Ze4lcLld91w0PwnTKqiYy5-vmauruNJCOFXugO1vc3qytA8m8631LhcbrDBHLLS5ieeeA0dXxDTacTPATb1TfM45ahj3Sb0DBA8pefMxuDRkrUAtMporaN4zJ-EenrB0TGlMXklF-w0XQnXezB4_sZmSKlopH4pfSMBKjj_fScP1OlJydKkrbLQzMNHzY6IIgN_xv2Ed-Rf4_I9jdGC8r3BV4YLlQYklKaFfObr86Ew 
```

### Access Token Response

#### Successful Response

The authorization server issues an access token and optional refresh token and ID Token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code:

* **access_token**

  REQUIRED. The access token issued by the authorization server.


* **token_type**

  REQUIRED. The type of the token issued. It MUST be `bearer`.


* **expires_in**

  RECOMMENDED. The lifetime in seconds of the access token.


* **refresh_token**

  OPTIONAL, if scope `offline_access` is not included; otherwise, REQUIRED. The refresh token, which can be used to obtain new access tokens using the same authorization.


* **scope**

  OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED.


* **id_token**

  OPTIONAL, if scope `openid` is not included; otherwise, REQUIRED. ID Token value associated with the authenticated session.


The following is a non-normative example of a successful Token Response (the ID Token signature in the example can be verified with the key at [Appendix A.1 Authorization Server RSA Key](#appendix-a1-authorization-server-rsa-key)).

```http 
HTTP/1.1 200 OK
Content-Type: application/json
```
```json
{
  "access_token": "SlAV32hkKG",
  "token_type": "Bearer",
  "refresh_token": "8xLOxBtZp8",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.eyJpc3MiOiJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwic3ViIjoiYWJiZTBiZDYtYTE4Ny00MmRjLThhYTQtYWI2NWU0NGZkZTYzIiwiYXVkIjoiczZCaGRSa3F0MyIsIm5vbmNlIjoibi0wUzZfV3pBMk1qIiwiZXhwIjoxMzExMjgxOTcwLCJpYXQiOjEzMTEyODA5NzB9.opu_go5CTK3o4BesX7-hChKLvr5oOKzkH_j3D_luWCFPbejZP86tWxJDT_HxrTHvvxBgopLcYLoDOHPCY2RHDvEjyuYvejy93sUQf3YA7O58aPzlSweDbpbjARq45tjEbY5W7MHHiBcg2Az9s2UZOHY6Hpnc5cEIz4S6geD25pSiSGzmLic8kiZtP8SjDEp5vNJedOhjfEp1-xVyOGSujd9pz4jWRNnN61mq0dWwVFwD9pqdXWvyLVcndvEzXTgSLMTX98gd1wykbGef9Ud-1an37_7JFdOB5pROAZDmeLQvfCeaKy6GJF5fftA1aSlC1u-vVDuZP4Ip8zZdM5ShiA"
}
```
#### Error Response

The authorization server responds with an HTTP 400 (Bad Request) status code (unless specified otherwise) and includes the following parameters within the response body:

* **error**

  REQUIRED. A single ASCII error code from the following:

    * **_invalid_request_**

      The request is missing a required parameter, includes an unsupported parameter value (other than grant type), repeats a parameter, includes multiple credentials, utilizes more than one mechanism for authenticating the client, or is otherwise malformed. In addition, this error can be returned when resource owner denied the request (e.g., the user does not give their consent for the requested authorization).

    * **_invalid_client_**

      Client authentication failed (e.g., unknown client, no client authentication included, or unsupported authentication method). The authorization server MAY return an HTTP 401 (Unauthorized) status code to indicate which HTTP authentication schemes are supported. If the client attempted to authenticate via the `Authorization` request header field, the authorization server MUST respond with an HTTP 401 (Unauthorized) status code and include the `WWW-Authenticate` response header field matching the authentication scheme used by the client.

    * **_invalid_grant_**

      The provided authorization grant authorization code is invalid, expired, revoked, does not match the redirection URI used in the authorization request, or was issued to another client.

    * **_unauthorized_client_**

      The authenticated client is not authorized to use this authorization grant type.

    * **_unsupported_grant_type_**

      The authorization grant type is not supported by the authorization server.


* **error_description**

  OPTIONAL. Human-readable ASCII text providing additional information, used to assist the client developer in understanding the error that occurred. Values for the `error_description` parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.


The parameters are included in the entity-body of the HTTP response using the `application/json` media type.

`error_description` parameter is not a valid field to take decisions on the developer side, as its value may vary depending on the server implementation, language, format or context. Therefore, the developer should base their logic only on the `error` parameter. Moreover, `error_description` message is not intended to be displayed to the user, but only to help the developer debug the issue. If the developer needs to provide a custom error message to the user, they should use their own logic to map the error code to a suitable message, taking into account the user’s language, tone and level of detail.


The following is a non-normative example Token Error Response:
```http
HTTP/1.1 400 Bad Request
Content-Type: application/json
```
```json
{
  "error": "invalid_grant"
}
```


## Authorization using the OpenID Connect Client-Initiated Backchannel Authentication Flow (CIBA)

The [Client-Initiated Backchannel Authentication (CIBA)](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html) flow is used to initiate an out-of-band authentication of the end-user. This is done by sending an HTTP POST message directly from the Client to the OpenID Provider's Backchannel Authentication Endpoint, using a request defined in the following subsections.

Communication with the Backchannel Authentication Endpoint MUST utilize TLS.

CIBA allows the Client to get the authentication result in three ways: poll, ping, or push. This profile only allows poll mode. In the Poll mode, the authentication result is retrieved by the Client by polling the OP's token endpoint using the new grant type.

### Authentication Request

An authentication request is composed of the following parameters and MAY contain additional parameters defined by extension or profile:

* **scope**

  REQUIRED. A space delimited and a case-sensitive list of ASCII strings for OAuth 2.0 scope values. Consistent with that, CIBA authentication requests MUST therefore contain the `openid` scope value. More information about scope values as described in Scope Parameter Section.


* **login_hint**

  REQUIRED. A hint to the OpenID Provider regarding the end-user for whom authentication is being requested. The value may contain a phone number, ip, etc., which identifies the end-user to the Authorization Server. The value may be directly collected from the user by the Client before requesting authentication at the Authorization Server, for example, but may also be obtained by other means.

  The `login_hint` value will follow the format: `identifier_type:identifier_value`.

  The defined values are for each identifier type:

    * **_tel_**

      For phone numbers. The `login_hint` must be a tel URI as defined in [RFC 3966](https://www.rfc-editor.org/info/rfc3966) for global phone numbers without visual separators in [E.164](https://www.itu.int/rec/T-REC-E.164-201011-I/en) format. For example, `tel:+34666666666`.

    * **_ipport_**

      For IPv4 and IPv6 addresses, that can optionally include a port. For example, `ipport:80.90.34.2:16790`, `ipport:80.90.34.2`, `ipport:[2001:db8::1]:8080` or `ipport:[2001:db8::1]`.


Because in the CIBA flow, the Authorization Server does not have an interaction with the end-user through the consumption device, it is REQUIRED that the Client provides a `login_hint`.

An authentication request is made using the HTTP POST method with the aforementioned parameters in the `application/x-www-form-urlencoded` format and a character encoding of UTF-8 in the HTTP request entity-body. When applicable, additional parameters required by the given client authentication method are also included (e.g. JWT assertion-based client authentication uses `client_assertion` and `client_assertion_type`).

The client MUST authenticate with the authorization server as described in [Client Authentication Section](#client-authentication).

The following is a non-normative example of an authentication request (with line wraps within values for display purposes only):

```http
POST /bc-authorize HTTP/1.1
Host: server.example.com
Content-Type: application/x-www-form-urlencoded

scope=openid%20example-scope&
login_hint=ipport%3A80.90.34.2&
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&
client_assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzNkJoZFJrcXQzIiwiYXVkIjoiaHR0cHM6Ly9zZXJ2ZXIuZXhhbXBsZS5jb20iLCJzdWIiOiJzNkJoZFJrcXQzIiwiZXhwIjoxMzAwODE5MzgwLCJpYXQiOjEzMDA4MTkwODAsImp0aSI6ImUyMjA5MTNmLTM5ZjItNDEwNi05ZmU0LWI3YmEzNGJkZjJkNSJ9.khzDX5keZQ6EJiJd8upzUMvAZtH8-PypGrWJYeA-Upx1MRDEr54U0V3T2-XZ93PbQE0waMyn25Z-xiDsCMVazYvOEa_Hz5_mE2x1aVZ8m86Ze4lcLld91w0PwnTKqiYy5-vmauruNJCOFXugO1vc3qytA8m8631LhcbrDBHLLS5ieeeA0dXxDTacTPATb1TfM45ahj3Sb0DBA8pefMxuDRkrUAtMporaN4zJ-EenrB0TGlMXklF-w0XQnXezB4_sZmSKlopH4pfSMBKjj_fScP1OlJydKkrbLQzMNHzY6IIgN_xv2Ed-Rf4_I9jdGC8r3BV4YLlQYklKaFfObr86Ew
```

### Authentication Response


#### Successful Response

If the Authentication Request is validated, the OpenID Provider will return an HTTP 200 OK response to the Client to indicate that the authentication request has been accepted and is going to be processed. The body of this response will contain:

* **auth_req_id**

  REQUIRED. This is a unique identifier to identify the authentication request made by the Client. It MUST contain sufficient entropy (a minimum of 128 bits while 160 bits is recommended) to make brute force guessing or forgery of a valid `auth_req_id` computationally infeasible - the means of achieving this are implementation-specific, with possible approaches including secure pseudorandom number generation or cryptographically secured self-contained tokens. The OpenID Provider MUST restrict the characters used to 'A'-'Z', 'a'-'z', '0'-'9', '.', '-' and '_', to reduce the chance of the client incorrectly decoding or re-encoding the `auth_req_id`; this character set was chosen to allow the server to use unpadded base64url if it wishes. The identifier MUST be treated as opaque by the client.


* **expires_in**

  REQUIRED. A JSON number with a positive integer value indicating the expiration time of the `auth_req_id` in seconds since the authentication request was received.


* **interval**

  OPTIONAL. A JSON number with a positive integer value indicating the minimum amount of time in seconds that the Client MUST wait between polling requests to the token endpoint. If no value is provided, clients MUST use 5 as the default value.


Clients MUST ignore unrecognized response parameters.


The following is a non-normative example of an authentication response:

```http
HTTP/1.1 200 OK 
Content-Type: application/json 
```
```json
{
  "auth_req_id": "1c266114-a1be-4252-8ad1-04986c5b9ac1",
  "expires_in": 120,
  "interval": 2
} 
```


#### Error Response

An Authentication Error Response is returned directly from the Backchannel Authentication Endpoint in response to the Authentication Request sent by the Client. The applicable error codes are detailed below.

Authentication Error Responses are sent in the same format as Token Error Responses, i.e. the HTTP response body uses the `application/json` media type with the following parameters:

* **error**

  REQUIRED. A single ASCII error code from one present in the list below.


* **error_description**

  OPTIONAL. Human-readable ASCII text providing additional information, used to assist the client developer in understanding the error that occurred. Values for the `error_description` parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.


List of authentication error codes associated with HTTP Errors:

* HTTP 400 Bad Request:

    * **_invalid_request_**

      The request is missing a required parameter, includes an invalid parameter value, includes a parameter more than once, contains more than one of the hints, or is otherwise malformed.

    * **_invalid_scope_**

      The requested scope is invalid, unknown, or malformed.

    * **_unknown_user_id_**

      The OpenID Provider is not able to identify which end-user the Client wishes to be authenticated by means of the `login_hint` provided in the request.

    * **_unauthorized_client_**

      The Client is not authorized to use this authentication flow.


* HTTP 401 Unauthorized:

    * **_invalid_client_**

      Client authentication failed (e.g., invalid client credentials, unknown client, no client authentication included, or unsupported authentication method).


* HTTP 403 Forbidden:

    * **_access_denied_**

      The resource owner or OpenID Provider denied the request (e.g., the user does not give their consent for the requested authorization). Note that as the authentication error response is received prior to any user interaction, such an error would only be received if a resource owner or OpenID Provider had made a decision to deny a certain type of request or requests from a certain type of client.

Note, when a client receives a 4xx response code with a JSON payload then it should inspect the payload to determine the error rather than relying on the HTTP status code alone.

`error_description` parameter is not a valid field to take decisions on the developer side, as its value may vary depending on the server implementation, language, format or context. Therefore, the developer should base their logic only on the `error` parameter.

The following is a non-normative example from an authentication error response:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json
```
```json
{
  "error": "unknown_user_id"
}
```


### Access Token Request

In the Poll mode, the Client polls the token endpoint at a reasonable interval, which MUST NOT be more frequent than the minimum interval provided by the OpenID Provider via the `interval` parameter (if provided).

The polling interval is measured from the instant when a polling request is sent. A Client MUST NOT send two overlapping requests with the same `auth_req_id`. Rather the client must always wait until receiving the response to the previous request before sending out the next request. If the interval has passed when the response is received, then the client may immediately send out the next request.

An Authorization Server experiencing heavy load may return an HTTP 503 with a Retry-After response header of HTTP/1.1. Clients should respect such headers and only retry the token request after the time indicated in the header.

The Client makes an "HTTP POST" request to the token endpoint by sending the following parameters using the `application/x-www-form-urlencoded` format with a character encoding of UTF-8 in the HTTP request entity-body:

* **grant_type**

  REQUIRED. Value MUST be `urn:openid:params:grant-type:ciba`.


* **auth_req_id**

  REQUIRED. It is the unique identifier to identify the authentication request (transaction) made by the Client. The Authorization Server MUST check whether the `auth_req_id` was issued to this Client in response to an AUthentication Request. Otherwise, an error MUST be returned.

The client MUST authenticate with the authorization server as described in [Client Authentication Section](#client-authentication).

The following is a non-normative example of a token request (with line wraps within values for display purposes only):

```http
POST /token HTTP/1.1 
Host: server.example.com 
Content-Type: application/x-www-form-urlencoded 
 
grant_type=urn%3Aopenid%3Aparams%3Agrant-type%3Aciba& 
auth_req_id=1c266114-a1be-4252-8ad1-04986c5b9ac1& 
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer& 
client_assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzNkJoZFJrcXQzIiwiYXVkIjoiaHR0cHM6Ly9zZXJ2ZXIuZXhhbXBsZS5jb20iLCJzdWIiOiJzNkJoZFJrcXQzIiwiZXhwIjoxMzAwODE5MzgwLCJpYXQiOjEzMDA4MTkwODAsImp0aSI6ImUyMjA5MTNmLTM5ZjItNDEwNi05ZmU0LWI3YmEzNGJkZjJkNSJ9.khzDX5keZQ6EJiJd8upzUMvAZtH8-PypGrWJYeA-Upx1MRDEr54U0V3T2-XZ93PbQE0waMyn25Z-xiDsCMVazYvOEa_Hz5_mE2x1aVZ8m86Ze4lcLld91w0PwnTKqiYy5-vmauruNJCOFXugO1vc3qytA8m8631LhcbrDBHLLS5ieeeA0dXxDTacTPATb1TfM45ahj3Sb0DBA8pefMxuDRkrUAtMporaN4zJ-EenrB0TGlMXklF-w0XQnXezB4_sZmSKlopH4pfSMBKjj_fScP1OlJydKkrbLQzMNHzY6IIgN_xv2Ed-Rf4_I9jdGC8r3BV4YLlQYklKaFfObr86Ew
```


### Access Token Response


#### Successful Response

After receiving and validating a valid and authorized Token Request from the Client and when the end-user associated with the supplied `auth_req_id` has been authenticated and has authorized the request, the OpenID Provider returns a successful response. Once redeemed for a successful token response, the `auth_req_id` value that was used is no longer valid.

The authorization server issues an access token and optional refresh token and ID Token, and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code:

* **access_token**

  REQUIRED. The access token issued by the authorization server.


* **token_type**

  REQUIRED. The type of the token issued. It MUST be `bearer`.


* **expires_in**

  RECOMMENDED. The lifetime in seconds of the access token.


* **refresh_token**

  OPTIONAL, if scope `offline_access` is not included; otherwise, REQUIRED. The refresh token, which can be used to obtain new access tokens using the same authorization.


* **scope**

  OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED.


* **id_token**

  OPTIONAL, if scope `openid` is not included; otherwise, REQUIRED. ID Token value associated with the authenticated session.


The following is a non-normative example of a successful token response:

```http
HTTP/1.1 200 OK 
Content-Type: application/json 
Cache-Control: no-store 
```
```json
{
  "access_token": "G5kXH2wHvUra0sHlDy1iTkDJgsgUO1bN",
  "token_type": "Bearer",
  "refresh_token": "4bwc0ESC_IAhflf-ACC_vjD_ltc11ne-8gFPfA2Kx16",
  "expires_in": 120,
  "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjFlOWdkazcifQ.eyJpc3MiOiJodHRwOi8vc2VydmVyLmV4YW1wbGUuY29tIiwic3ViIjoiYWJiZTBiZDYtYTE4Ny00MmRjLThhYTQtYWI2NWU0NGZkZTYzIiwiYXVkIjoiczZCaGRSa3F0MyIsImV4cCI6MTMxMTI4MTk3MCwiaWF0IjoxMzExMjgwOTcwfQ.FV1R2jGvrssH3bH8XzsMixB2YDGQX8DvnlgCVQONMhPuOpfqbTVyDzDobVvLQjj8WUVnlXYIJdVgnBhy9FTpYpmh-KlJPil9f3RD74Mwm7FGvecF7nWdzNkXVX1IJxj7d5YATNz6kIwGI7KjqxITUS-ZkoYJgc3Tfs8wODvV0TJmU6vyZWQB3vkjDKnr-iC2wUhxv_enCwNoTteCe97N8mYl-0J9cWLw8F-vIlyXJD-8SlPZ2R_Ti5aEO7Q36G2tOyRacszWRGjBkiZTYFtRlWOAkLDQttu4GJgl5ORNoVcXRQdbBd-aSyVXsI7EESpJDNOmvziUbJkzPvOoAQxERQ"
} 
```


#### Error Response

The authorization server responds with an HTTP 400 (Bad Request) status code (unless specified otherwise) and includes the following parameters with the response:

* **error**

  REQUIRED. A single ASCII error code from the following:

    * **_invalid_request_**

      The request is missing a required parameter, includes an unsupported parameter value (other than grant type), repeats a parameter, includes multiple credentials, utilizes more than one mechanism for authenticating the client, or is otherwise malformed, or a Client continually polls quicker than the interval. If a client receives an `invalid_request` error, it must not make any further requests for the same `auth_req_id`.

    * **_invalid_client_**

      Client authentication failed (e.g., unknown client, no client authentication included, or unsupported authentication method). The authorization server MAY return an HTTP 401 (Unauthorized) status code to indicate which HTTP authentication schemes are supported. If the client attempted to authenticate via the `Authorization` request header field, the authorization server MUST respond with an HTTP 401 (Unauthorized) status code and include the `WWW-Authenticate` response header field matching the authentication scheme used by the client.

    * **_invalid_grant_**

      The provided authorization grant `auth_req_id` is invalid or was issued to another client.

    * **_unauthorized_client_**

      The authenticated client is not authorized to use this authorization grant type.

    * **_unsupported_grant_type_**

      The authorization grant type is not supported by the authorization server.

    * **_authorization_pending_**

      The authorization request is still pending as the end-user hasn't yet been authenticated.

    * **_slow_down_**

      A variant of `authorization_pending`, the authorization request is still pending and polling should continue, but the interval MUST be increased by at least 5 seconds for this and all subsequent requests.

    * **_expired_token_**

      The `auth_req_id` has expired. The Client will need to make a new Authentication Request. This error can be obtained when eventually no consent is granted by the user.

    * **_access_denied_**

      The end-user denied the authorization request.


* **error_description**

  OPTIONAL. Human-readable ASCII text providing additional information, used to assist the client developer in understanding the error that occurred. Values for the `error_description` parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.


The parameters are included in the entity-body of the HTTP response using the `application/json` media type.

The following is a non-normative example from a Token Error response:

```htt
HTTP/1.1 400 Bad Request
Content-Type: application/json
```
```json
{
  "error": "authorization_pending"
}
```


## Client Credentials Flow

The [OAuth 2.0 Client Credentials](https://datatracker.ietf.org/doc/html/rfc6749#section-4.4) grant type is used to obtain a 2-legged Access Token that does not represent a user. This grant type can only be used when no personal user data is processed, and it is only a valid option to access the CAMARA APIs for these specific scenarios.


### Access Token Request

The client makes a request to the token endpoint by adding the following parameters using the `application/x-www-form-urlencoded` format with a character encoding of UTF-8 in the HTTP request entity-body:

* **grant_type**

  REQUIRED. Value MUST be set to `client_credentials`.


* **scope**

  OPTIONAL. A space delimited and a case-sensitive list of ASCII strings.


The client MUST authenticate with the authorization server as described in [Client Authentication Section](#client-authentication).

For example, the client makes the following HTTP request using (with line wraps within values for display purposes only):

```http
POST /token HTTP/1.1 
Host: server.example.com 
Content-Type: application/x-www-form-urlencoded 

grant_type=client_credentials&
scope=example-scope&
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&
client_assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzNkJoZFJrcXQzIiwiYXVkIjoiaHR0cHM6Ly9zZXJ2ZXIuZXhhbXBsZS5jb20iLCJzdWIiOiJzNkJoZFJrcXQzIiwiZXhwIjoxMzAwODE5MzgwLCJpYXQiOjEzMDA4MTkwODAsImp0aSI6ImUyMjA5MTNmLTM5ZjItNDEwNi05ZmU0LWI3YmEzNGJkZjJkNSJ9.khzDX5keZQ6EJiJd8upzUMvAZtH8-PypGrWJYeA-Upx1MRDEr54U0V3T2-XZ93PbQE0waMyn25Z-xiDsCMVazYvOEa_Hz5_mE2x1aVZ8m86Ze4lcLld91w0PwnTKqiYy5-vmauruNJCOFXugO1vc3qytA8m8631LhcbrDBHLLS5ieeeA0dXxDTacTPATb1TfM45ahj3Sb0DBA8pefMxuDRkrUAtMporaN4zJ-EenrB0TGlMXklF-w0XQnXezB4_sZmSKlopH4pfSMBKjj_fScP1OlJydKkrbLQzMNHzY6IIgN_xv2Ed-Rf4_I9jdGC8r3BV4YLlQYklKaFfObr86Ew
```


### Access Token Response

If the access token request is valid and authorized, the authorization server issues an access token. A refresh token SHOULD NOT be included. If the request failed client authentication or is invalid, the authorization server returns an error.

#### Successful Response

The authorization server issues an access token and constructs the response by adding the following parameters to the entity-body of the HTTP response with a 200 (OK) status code:

* **access_token**

  REQUIRED. The access token issued by the authorization server.


* **token_type**

  REQUIRED. The type of the token issued. It MUST be `bearer`.


* **expires_in**

  RECOMMENDED. The lifetime in seconds of the access token.


* **scope**

  OPTIONAL, if identical to the scope requested by the client; otherwise, REQUIRED.


The following is a non-normative example Token Successful Response:
```http
HTTP/1.1 200 OK 
Content-Type: application/json;charset=UTF-8 
```
```json 
{
  "access_token":"2YotnFZFEjr1zCsicMWpAA",
  "token_type":"bearer",
  "expires_in":3600
} 
```


#### Error Response

The authorization server responds with an HTTP 400 (Bad Request) status code (unless specified otherwise) and includes the following parameters with the response:

* **error**

  REQUIRED. A single ASCII error code from the following:

    * **_invalid_request_**

      The request is missing a required parameter, includes an unsupported parameter value (other than grant type), repeats a parameter, includes multiple credentials, utilizes more than one mechanism for authenticating the client, or is otherwise malformed.

    * **_invalid_client_**

      Client authentication failed (e.g., unknown client, no client authentication included, or unsupported authentication method). The authorization server MAY return an HTTP 401 (Unauthorized) status code to indicate which HTTP authentication schemes are supported. If the client attempted to authenticate via the `Authorization` request header field, the authorization server MUST respond with an HTTP 401 (Unauthorized) status code and include the `WWW-Authenticate` response header field matching the authentication scheme used by the client.

    * **_invalid_grant_**

      The provided authorization grant authorization code is invalid, expired, revoked, does not match the redirection URI used in the authorization request, or was issued to another client.

    * **_unauthorized_client_**

      The authenticated client is not authorized to use this authorization grant type.

    * **_unsupported_grant_type_**

      The authorization grant type is not supported by the authorization server.

    * **_invalid_scope_**

      The requested scope is invalid, unknown, malformed, or exceeds the scope granted by the resource owner.


* **error_description**

  OPTIONAL. Human-readable ASCII text providing additional information, used to assist the client developer in understanding the error that occurred. Values for the `error_description` parameter MUST NOT include characters outside the set %x20-21 / %x23-5B / %x5D-7E.


The parameters are included in the entity-body of the HTTP response using the `application/json` media type.

`error_description` parameter is not a valid field to take decisions on the developer side, as its value may vary depending on the server implementation, language, format or context. Therefore, the developer should base their logic only on the `error` parameter.

The following is a non-normative example Token Error Response:

```http
HTTP/1.1 400 Bad Request
Content-Type: application/json
```
```json
{
  "error": "unauthorized_client"
}
```

## The Scope Parameter

Oauth scope values determine the specific CAMARA services being requested by the Service Provider, subject to the SP being registered to use those services.

The Authorization Request MUST contain the `scope` parameter, which is a space delimited, case-sensitive list of ASCII strings (scope values). Multiple scope values can be requested simultaneously, subject to the SP being registered to use those “scopes”.

For OpenID requests, the scope values MUST include `openid`, to indicate that the request is an OpenID Connect request, followed by other values depending on the specific CAMARA services being requested.

A purpose must be declared within the requested scope for the following grant types:

* _authorization_code_

* _urn:openid:params:grant-type:ciba_

No purpose is required for the `client_credentials` grant type, as this grant type can only be used when no personal user data is processed. The requested scope must be set directly to the value defined for the relevant endpoint within the OAS ("YAML") specification. "Wild card" scopes (i.e. specifying only the API name) are not valid for this grant type.

To declare a purpose when accessing the CAMARA APIs, it is strongly recommended to guarantee interoperability among Authorization Servers that the `scope` parameter is set to:

`dpv:<dpvValue>#<technicalParameter>`

* `<dpvValue>` is coming from [W3C DPV purpose definition](https://w3c.github.io/dpv/dpv/#vocab-purpose)

* `<technicalParameter>` must be either:

    * one technical scope to limit the request to one API endpoint (this technical scope is described in the API spec YAML file)

    * one API name to cover the complete API (This API name is not described in the API spec YAML file as technical scope) – In this case the request covers all technical scopes of the API.


## ID Token

The primary extension that OpenID Connect makes to OAuth 2.0 to enable End-Users to be Authenticated is the ID Token data structure. The ID Token is a security token that contains Claims about the Authentication of an End-User by an Authorization Server when using a Client, and potentially other requested Claims. The ID Token is represented as a [JSON Web Token (JWT)](https://www.rfc-editor.org/info/rfc7519). ID Tokens MUST be signed by Authorization Server using [JWS](https://www.rfc-editor.org/info/rfc7515).

The following Claims are used within the ID Token for all OAuth 2.0 flows used by OpenID Connect:

* **iss**

  REQUIRED. Issuer Identifier for the Issuer of the response (the Authorization Server). The iss value is a case-sensitive URL using the https scheme that contains scheme, host, and optionally, port number and path components and no query or fragment components.


* **sub**

  REQUIRED. Subject Identifier. A locally unique and never reassigned identifier within the Issuer for the End-User, which is intended to be consumed by the Client. It MUST NOT exceed 255 ASCII characters in length. The sub value is a case-sensitive string.


* **aud**

  REQUIRED. Audience(s) that this ID Token is intended for. It MUST contain the OAuth 2.0 `client_id` of the Relying Party as an audience value. It MAY also contain identifiers for other audiences. 	 In the general case, the aud value is an array of case-sensitive strings. In the common special case when there is one audience, the aud value MAY be a single case-sensitive string.


* **exp**

  REQUIRED. Expiration time on or after which the ID Token MUST NOT be accepted by the client when performing authentication with the OpenID Provider. The processing of this parameter requires that the current date/time MUST be before the expiration date/time listed in the value. Implementers MAY provide for some small leeway, usually no more than a few minutes, to account for clock skew. Its value is a [JSON](https://www.rfc-editor.org/info/rfc8259) number representing the number of seconds from 1970-01-01T00:00:00Z as measured in UTC until the date/time.


* **iat**

  REQUIRED. Time at which the JWT was issued. Its value is a JSON number representing the number of seconds from 1970-01-01T00:00:00Z as measured in UTC until the date/time.


* **auth_time**

  Time when the End-User authentication occurred. Its value is a JSON number representing the number of seconds from 1970-01-01T00:00:00Z as measured in UTC until the date/time. When a max_age request is made or when auth_time is requested as an Essential Claim, then this Claim is REQUIRED; otherwise, its inclusion is OPTIONAL.


* **nonce**

  String value used to associate a Client session with an ID Token, and to mitigate replay attacks. The value is passed through unmodified from the Authentication Request to the ID Token. If present in the ID Token, Clients MUST verify that the nonce Claim Value is equal to the value of the nonce parameter sent in the Authentication Request. If present in the Authentication Request, Authorization Servers MUST include a nonce Claim in the ID Token with the Claim Value being the nonce value sent in the Authentication Request. Authorization Servers SHOULD perform no other processing on nonce values used. The nonce value is a case-sensitive string.


* **acr**

  OPTIONAL. Authentication Context Class Reference. String specifying an Authentication Context Class Reference value that identifies the Authentication Context Class that the authentication performed satisfied.


* **amr**

  OPTIONAL. Authentication Methods References. JSON array of strings that are identifiers for authentication methods used in the authentication.


* **azp**

  OPTIONAL. Authorized party - the party to which the ID Token was issued. If present, it MUST contain the OAuth 2.0 Client ID of this party. The `azp` value is a case-sensitive string containing a StringOrURI value.


* **at_hash**

  OPTIONAL. Access Token hash value. Its value is the base64url encoding of the left-most half of the hash of the octets of the ASCII representation of the access_token value, where the hash algorithm used is the hash algorithm used in the alg Header Parameter of the ID Token's JOSE Header. For instance, if the alg is RS256, hash the access_token value with SHA-256, then take the left-most 128 bits and base64url-encode them. The `at_hash` value is a case-sensitive string.


ID Tokens MAY contain other Claims. Any Claims used that are not understood MUST be ignored.


## Client Authentication

This section defines a set of Client Authentication methods that are used by Clients to authenticate to the Authorization Server when using the Token or Backchannel Authorization Endpoint.

These Client Authentication methods are:

* **_private_key_jwt_**

  RECOMMENDED. Clients that have registered a public key sign a JWT using that key. The Client authenticates in accordance with JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants [OAuth.JWT] and Assertion Framework for OAuth 2.0 Client Authentication and Authorization Grants [OAuth.Assertions]. At least the Authorization Server MUST support JWS-signed Object with the RS256 algorithm. The JWT MUST contain the following REQUIRED Claim Values and MAY contain the following OPTIONAL Claim Values:

    * **iss**

      REQUIRED. Issuer. This MUST contain the `client_id` of the OAuth Client.

    * **sub**

      REQUIRED. Subject. This MUST contain the `client_id` of the OAuth Client.

    * **aud**

      REQUIRED. Audience. The aud (audience) Claim. Value that identifies the Authorization Server as an intended audience. The Authorization Server MUST verify that it is an intended audience for the token. The Audience MUST be the URL of the Authorization Server's Token Endpoint.

    * **jti**

      REQUIRED. JWT ID. A unique identifier for the token, which can be used to prevent reuse of the token. These tokens MUST only be used once, unless conditions for reuse were negotiated between the parties; any such negotiation is beyond the scope of this specification.

    * **exp**

      REQUIRED. Expiration time on or after which the JWT MUST NOT be accepted for processing.

    * **iat**

      OPTIONAL. Time at which the JWT was issued.

The JWT MAY contain other Claims. Any Claims used that are not understood MUST be ignored.

The authentication token MUST be sent as the value of the [OAuth.Assertions](https://www.rfc-editor.org/info/rfc7521) `client_assertion` parameter.

The value of the [OAuth.Assertions](https://www.rfc-editor.org/info/rfc7521) `client_assertion_type` parameter MUST be `urn:ietf:params:oauth:client-assertion-type:jwt-bearer`, per [OAuth.JWT](https://www.rfc-editor.org/info/rfc7523).


* **_client_secret_basic_**

  OPTIONAL. Clients that have received a client_secret value from the Authorization Server MAY authenticate with the Authorization Server using the HTTP Basic authentication scheme as defined in [RFC 2617](https://www.rfc-editor.org/info/rfc2617).


## References

* [Data Privacy Vocabulary (DPV)](https://w3c.github.io/dpv/dpv/)
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


## Appendix A. RSA Key Used in Examples

### Appendix A.1. Authorization Server RSA Key

The following RSA public key, represented in JWK format, can be used to validate client assertion signatures:

```json
{
  "kty": "RSA",
  "e": "AQAB",
  "alg": "RS256",
  "use": "sig",
  "kid": "1e9gdk7",
  "n": "o4hb8UZ-dRxvSh7FNkcYhqP-xmgGSIp_y4Vnk2QFADLe-pYpo2L4FzQMv3mjXAN7eFuZJkBQIx2F3k58_XTO_Ph7NglssPgP90SutFAsdRWHdoRwDmoEwbwLTL-xTGEtd0imbRQMBig5yldhyh_hsuuHQtMfa_yWsM4XuZf82-UZxip3UYvSU7IuhBQ2-zlNUN5ckP9e2s9tDSdUnCxjLDhcogACD4kiP26G2m43jyat60N8CtU02adwHXRgdVKSl0bhVSoJeYai2YM3Vwswh5NfH11BvaB6hhisdOszhVdTxVBsCsf_nsnp_INsPTys3yTE2IGU5IB2vNYLk33K5Q"
}
```

### Appendix A.2. Client RSA Key

The following RSA public key, represented in JWK format, can be used to validate the ID Token signatures:

```json
{
  "kty": "RSA",
  "e": "AQAB",
  "alg": "RS256",
  "use": "sig",
  "n": "u1SU1LfVLPHCozMxH2Mo4lgOEePzNm0tRgeLezV6ffAt0gunVTLw7onLRnrq0_IzW7yWR7QkrmBL7jTKEn5u-qKhbwKfBstIs-bMY2Zkp18gnTxKLxoS2tFczGkPLPgizskuemMghRniWaoLcyehkd3qqGElvW_VDL5AaWTg0nLVkjRo9z-40RQzuVaE8AkAFmxZzow3x-VJYKdjykkJ0iT9wCS0DRTXu269V264Vf_3jvredZiKRkgwlL9xNAwxXFg0x_XFw005UWVRIkdgcKWTjpBP2dPwVZ4WWC-9aGVd-Gyn1o0CLelf4rEjGoXbAAEgAqeGUxrcIlbjXfbcmw"
}
```
