# Identity and Consent Management Examples

## Specifying One Purpose

---
**Note**

Access tokens content or structure are not part of the OAuth2 nor the OIDC standard. In [RFC7662](https://datatracker.ietf.org/doc/html/rfc7662) only the field `active` is REQUIRED.
`scope` and all other fields are optional. [JSON Web Token](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1) defines some common claims.
RFC7662 response values serve as an **example** how an access token might look like. These access tokens might contain additional fields carrying what Camara needs regarding "purpose"

The `openid` scope is needed in the request to specify that the request is an OpenID Connect request. However, there is no explicit requirement to include the `openid` scope in the response.

---
**Note**

This document uses the response of the token-introspection endpoint as per RFC7662 to describe an access token.
This document does not say that the access token is self-contained or not.

---

### Purpose as a scope: Requesting one purpose, two scopes of the same API

#### OIDC authorization code flow with one purpose as scope

See [OIDC Authentication Request](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest)

```
GET /authorize?
response_type=code
&scope=openid%20dpv%3AFraudPreventionAndDetection%20sim-swap%3Acheck%20sim-swap%3Aretrieve-date
&client_id=s6BhdRkqt3
&state=af0ifjsldkj
&redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
```

##### Successful response redirecting the user agent 

See [OIDC Successful Authentication Response](https://openid.net/specs/openid-connect-core-1_0.html#AuthResponse)

```
HTTP/1.1 302 Found 
Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=af0ifjsldkj
```

##### Access token request

See [OIDC Token Request](https://openid.net/specs/openid-connect-core-1_0.html#TokenRequest)


```
POST /token HTTP/1.1
Host: server.example.com 
Content-Type: application/x-www-form-urlencoded 

grant_type=authorization_code&code=SplxlOBeZQQYbYS6WxSbIA
&redirect_uri=https%3A%2F%2Fclient%2Eexample%2Ecom%2Fcb
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3Mi......
```

##### Successful response

See [OIDC Successful Token Response](https://openid.net/specs/openid-connect-core-1_0.html#TokenResponse)

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "SlAV32hkKG",
  "token_type": "Bearer",
  "refresh_token": "8xLOxBtZp8",
  "expires_in": 3600,
  "id_token": "eyJhbGciOiJSUz....",
  "scope": "dpv:FraudPreventionAndDetection sim-swap:retrieve-date"
}
```
In this example, scopes differ from the one defined in the /authorize. If scopes are identical in /authorize and in the successful response, parameter scope may not be returned.


#### CIBA (unsigned) authentication request with one purpose and two scopes

See [CIBA authentication request](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.7.1)

```
POST /bc-authorize HTTP/1.1
Host: server.example.com
Content-Type: application/x-www-form-urlencoded

scope=openid%20dpv%3AFraudPreventionAndDetection%20sim-swap%3Acheck%20sim-swap%3Aretrieve-date
&login_hint=tel%3A%2B34666666666
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3Mi......
```


##### Successful response

See [CIBA Successful Authentication Response](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.7.3)

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
  "auth_req_id": "3f7b2e8a-9cde-4f3b-8b12-1a2b3c4d5e6f",
  "expires_in": 120,
  "interval": 2
}
```
The Client MUST keep the `auth_req_id` in order to use it when making a token request in Poll mode.
Please note that the values for `expires_in` and `interval` may be different from those in the example.

##### Access token request

See [CIBA Token Request](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.10.1)


```
POST /token HTTP/1.1
Host: server.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aopenid%3Aparams%3Agrant-type%3Aciba
&auth_req_id=3f7b2e8a-9cde-4f3b-8b12-1a2b3c4d5e6f
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3Mi......   
```

##### Successful response

See [CIBA Successful Token Response](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.10.1.1)

```       
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
  "access_token": "G5kXH2wHvUra0sHlDy1iTkDJgsgUO1bN",
  "token_type": "Bearer",
  "refresh_token": "4bwc0ESC_IAhflf-ACC_vjD_ltc11ne-8gFPfA2Kx16",
  "expires_in": 120,
  "id_token": "eyJhbGciOiJSUzI1NiIsImtp...."
}
```

##### JWT Bearer Flow

##### JWT Bearer Token Request

See [JWT Bearer Token Request](https://datatracker.ietf.org/doc/html/rfc7523#section-4)

```
POST /token.oauth2 HTTP/1.1
Host: server.example.com
Content-Type: application/x-www-form-urlencoded

grant_type=urn%3Aietf%3Aparams%3Aoauth%3Agrant-type%3Ajwt-bearer
&assertion=eyJhbGciOiJFUzI1NiIsImtpZCI6IjE2In0.
eyJpc3Mi[...omitted for brevity...].
J9l-ZhwP[...omitted for brevity...]
```
The requested purpose and scopes are included in the JWT assertion `scope` claim. The `scope` parameter MUST NOT be specified for JWT-Bearer Flow as per [CAMARA Security and Interoperability Profile](CAMARA-Security-Interoperability.md#jwt-bearer-flow) 

##### Successful response

```
HTTP/1.1 200 OK
Content-Type: application/json
Cache-Control: no-store

{
  "access_token": "G5kXH2wHvUra0sHlDy1iTkDJgsgUO1bN",
  "token_type": "Bearer",
  "expires_in": 120
}
```