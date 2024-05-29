# Identity and Consent Management Examples

## Specifying One Purpose

---
**Note**

Access tokens content or structure are not part of the OAuth2 nor the OIDC standard. In [RFC7662](https://datatracker.ietf.org/doc/html/rfc7662) only the field `active` is REQUIRED.
`scope` and all other fields are optional. [JSON Web Token](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1) defines some common claims.
RFC7662 response values serve as an **example** how an access token might look like. These access tokens might contain additional fields carrying what Camara needs regarding "purpose"

The scope `openid` is needed only in the request to specify that the request is an OpenId request. The scope `openid` is not needed in the access token.

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

#### Successful response redirecting the user agent 

See [OIDC Successful Authentication Response](https://openid.net/specs/openid-connect-core-1_0.html#AuthResponse)

(with line wraps within values for display purposes only)

```
HTTP/1.1 302 Found 
Location: https://client.example.com/cb?code=SplxlOBeZQQYbYS6WxSbIA&state=af0ifjsldkj
```

#### Access token request

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

#### Successful response

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
  "scope": "openid dpv:FraudPreventionAndDetection sim-swap:check sim-swap:retrieve-date"
}
```

#### RFC9101 request object with one purpose as scope

```
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "response_type": "code",
  "client_id": "s6BhdRkqt3",
  "redirect_uri": "https://client.example.org/cb",
  "scope": "openid dpv:FraudPreventionAndDetection sim-swap:check sim-swap:retrieve-date",
  "state": "af0ifjsldkj",
  "nonce": "n-0S6_WzA2Mj",
  "max_age": 86400,
  "exp": 1419356238,
  "iat": 1419350238
}
```

#### CIBA authentication request with one purpose and two scopes

See [CIBA authentication request](https://openid.net/specs/openid-client-initiated-backchannel-authentication-core-1_0.html#rfc.section.7.1)

```
POST /bc-authorize HTTP/1.1
   Host: server.example.com
   Content-Type: application/x-www-form-urlencoded

   scope=openid%20dpv%3AFraudPreventionAndDetection%20sim-swap%3Acheck%20sim-swap%3Aretrieve-date&
   client_notification_token=8d67dc78-7faa-4d41-aabd-67707b374255&
   binding_message=W4SCT&
   login_hint=tel%3A%2B34666666666&
   client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3A
   client-assertion-type%3Ajwt-bearer&
   client_assertion=eyJraWQiOiJsdGFjZXNidyIsImFsZyI6IkVTMjU2In0.eyJ
   pc3MiOiJzNkJoZFJrcXQzIiwic3ViIjoiczZCaGRSa3F0MyIsImF1ZCI6Imh0dHB
   zOi8vc2VydmVyLmV4YW1wbGUuY29tIiwianRpIjoiYmRjLVhzX3NmLTNZTW80RlN
   6SUoyUSIsImlhdCI6MTUzNzgxOTQ4NiwiZXhwIjoxNTM3ODE5Nzc3fQ.Ybr8mg_3
   E2OptOSsA8rnelYO_y1L-yFaF_j1iemM3ntB61_GN3APe5cl_-5a6cvGlP154XAK
   7fL-GaZSdnd9kg
```






