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

```
GET /authorize?
    response_type=code
    &scope=openid%20dpv%3AFraudPreventionAndDetection%20sim-swap%3Acheck%20sim-swap%3Aretrieve-date
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
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


## Using Rich Authorization Request to convey purpose

---
**Note**

Please note that Rich Authorization Request examples are here to help in the discussion how RAR might be used in Camara **in the FUTURE**

---

Please note that the `type` in the ONE purpose requests is `org.camaraproject.purpose` and thus NOT API-specific.

In the TWO-or-more purpose request the `type` proposed to be API-specific.

---

### Using Rich Authorization Request to convey one purpose


```
GET /authorize?
    response_type=code
    &authorization_details=%5B%7B%22type%22%3A%22org.camaraproject.purpose%22%2C%22purpose%22%3A%22dpv%3AFraudPreventionAndDetection%22%7D%5D
    &scope=openid%20sim-swap:check%20sim-swap:retrieve-date
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
```

#### RFC9101 request object `authorization_details` one purpose

```
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "response_type": "code",
  "client_id": "s6BhdRkqt3",
  "redirect_uri": "https://client.example.org/cb",
  "authorization_details": "[{"type":"org.camaraproject.purpose","purpose":"dpv:FraudPreventionAndDetection"}]"
  "scope": "openid sim-swap:check sim-swap:retrieve-date",
  "state": "af0ifjsldkj",
  "nonce": "n-0S6_WzA2Mj",
  "max_age": 86400
}
```


### Use Rich Authorization Request to convey two purpose

```
[
  {
    "type": "org.camaraproject.sim-swap",
    "purpose": "dpv:Advertising",
    "locations": ["/retrieve-date"]
  },
  {
    "type": "org.camaraproject.sim-swap",
    "purpose": "dpv:FraudPreventionAndDetection",
    "locations": ["/check"]
  }
]
```

```
GET /authorize?
    response_type=code
    &authorization_details=%5B%7B%22type%22%3A%22org.camaraproject.purpose%22%2C%22purpose%22%3A%22dpv%3AAdvertising%22%2C%22location%22%3A%22%2Fretrieve-date%22%7D%2C%7B%22type%22%3A%22org.camaraproject.purpose%22%2C%22purpose%22%3A%22dpv%3AFraudPreventionAndDetection%22%2C%22location%22%3A%22%2Fcheck%22%7D%5D
    &scope=openid
    &client_id=s6BhdRkqt3
    &state=af0ifjsldkj
    &redirect_uri=https%3A%2F%2Fclient.example.org%2Fcb HTTP/1.1
Host: server.example.com
```

#### RFC9101 request object RAR two purpose

```
{
  "iss": "s6BhdRkqt3",
  "aud": "https://server.example.com",
  "response_type": "code",
  "client_id": "s6BhdRkqt3",
  "redirect_uri": "https://client.example.org/cb",
  "authorization_details": [
    {
      "type": "org.camaraproject.purpose",
      "purpose": "dpv:Advertising",
      "location": "/retrieve-date"
    },
    {
      "type": "org.camaraproject.purpose",
      "purpose": "dpv:FraudPreventionAndDetection",
      "location": "/check"
    }
  ],
  "scope": "openid",
  "state": "af0ifjsldkj",
  "nonce": "n-0S6_WzA2Mj",
  "max_age": 86400
}
```


#### Response on introspecting an access token with two purpose and RAR

```
{
  "active": true,
  "client_id": "s6BhdRkqt3",
  "username": "jdoe",
  "authorization_details": [
    {
      "type": "org.camaraproject.sim-swap",
      "purpose": "dpv:Advertising",
      "locations": ["/retrieve-date"]
    },
    {
      "type": "org.camaraproject.sim-swap",
      "purpose": "dpv:FraudPreventionAndDetection",
      "locations": ["/check"]
    }
  ],
  "sub": "Z5O3upPC88QrAjx00dis",
  "aud": "https://protected.example.net/resource",
  "iss": "https://server.example.com/",
  "exp": 1419356238,
  "iat": 1419350238
}
```





