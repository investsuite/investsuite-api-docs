---
title: First steps
---

## Request a key

To start using the InvestSuite API you need authentication data. To get authentication data please reach out [investsuite.com/contact](https://www.investsuite.com/contact). 

## Domains

Environment | Domain | Description
--- | --- | ---
Production API | TBD | API live environment
Production Admin Console | TBD | Admin console for the live API
Test API | [https://api.uat.investsuite.com](https://api.uat.investsuite.com) | API test environment
Test Admin Console | [https://console.uat.investsuite.com](https://console.uat.investsuite.com) | Admin console for the test API 

## API Authentication

Authenticate against the API to receive a JSON Web Token (JWT). Learn more about JWT at [jwt.io](https://jwt.io). 

!!! machine-to-machine
    Requests should not be directly sent from your app or website, as your authentication data may be exposed in transit. All requests are required to be made via an HTTPS connection; requests made over plain HTTP will fail.

When you successfully authenticate you receive an `access_token` and a `refresh_token`. Add the `access_token`to the HTTP headers in all subsequent requests. The `access_token` has a limited lifetime. The duration is added to the response body in the `expires_at`field, e.g `expires_at: 300`. To silently prolong the session without requiring authentication data request renewal using the `/refresh_token`endpoint.

**Endpoint**

```
POST /auth
```

**Parameter**

Field name | Description | Required
--- | --- | ---
`user` | The access key required to retrieve a JWT. | ✔️
`password` | The secret required to retrieve a JWT. | ✔️

**Example request**

```zsh
curl -X POST \
    -H "Content-Type: application/json" \
    -d '{"user":"{access_key}","password":"{secret}"}'\
    https://{domain}/auth
```




