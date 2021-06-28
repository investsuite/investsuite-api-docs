---
title: Authentication
---

Authenticate against the API to receive a JSON Web Token (JWT). Learn more about JWT at [jwt.io](https://jwt.io). To authenticate you need an access key and a secret. Reach out to your InvestSuite representative and we will set you up in no time.

!!! Warning
    Requests should not be directly sent from your app or website, as your authentication data may be exposed in transit. All requests are required to be made via an HTTPS connection; requests made over plain HTTP will fail.

When you successfully authenticate you receive an `access_token` and a `refresh_token`. Add the `access_token`to the HTTP headers in all subsequent requests. The `access_token` has a limited lifetime. The duration is added to the response body in the `expires_at`field, e.g `expires_at: 300`. To silently prolong the session without requiring authentication data request renewal using the `/refresh_token`endpoint.

=== "Request"

    ```HTTP hl_lines="1"
    POST /auth HTTP/1.1
    Host: public-api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json

    {
        "user": "{access_key}",
        "password": "{secret}"
    }

    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "access_token":"eyJ0eXAiOiJKV1QiL...",
        "token_type":"Bearer",
        "expires_in":"300",
        "refresh_token":"eyJraWQiOiJEb..."
    }
    ```

