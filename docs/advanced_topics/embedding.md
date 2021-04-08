# Embedding Additional Entities in API Responses

??? Summary
    Embedding of referenced resources can be achieved using the `?embed=` query string parameter, which adds an optional response field `_embedded` at the root of the response object containing a dictionary of the embedded `EntityID` and the data for that entity.

## Introduction

The InvestSuite API supports optional embedding of entities by `EntityID`, a feature which is loosely based on [Zalando's RESTful API Performance Guideline #158](https://opensource.zalando.com/restful-api-guidelines/#158). Embedding enables API clients to merge referenced entities directly into a single response, which reduces the amount of (sequential) round trips required to fetch data from InvestSuite's backend. This feature is particularly convenient in use cases where data from multiple entities is joined together, a common operation when preparing data for reporting purposes or in specific front end views.

In order to embed certain field(s), the `?embed=` query string parameter needs to be provided in `GET` requests. This parameter can be set to one or more field names that are available in the response object (and are references to other entities). The response will be extended with an `_embedded` key at the root level, which contains a full rendition of the referenced entities. This provides a useful level of flexibility in API responses, allowing clients to directly retrieve related data with a single request.

!!! Hint
    Embedding works for any response field value that is an (array of) `EntityID`s, which can be recognized by the globally-unique identifier that follows below regex (such as a `UserID` like `U01234567890123456789012345`). This regex translates to a single alphabet letter identifying the type of entity, followed by a 26-character ULID based on Crockford’s base32 character set.

    ```
    ^[A-Z][0-9A-HJKMNP-TV-Z]{26}$
    ```

## Example with a single entity

Suppose an API client requires both data for a specific portfolio (`P01234567890123456789012345`) _and_ data for the user that owns this particular portfolio. Without embedding, this would be a sequential two-step process, where the client first retrieves the portfolio in question:

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios/P01234567890123456789012345 HTTP/1.1
    Host: api.investsuite.com
    Accept: application/json
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "readable_by": [
            "U01234567890123456789012345"
        ],
        "modifiable_by": [
            "U01234567890123456789012345"
        ],
        "external_id": "my_exeternal_portfolio_id",
        "name": "Example Portfolio",
        "owned_by_user_id": "U01234567890123456789012345",
        "portfolio": {
            ...
        },
        ...
        "id": "P01234567890123456789012345",
        ...
    }
    ```

And then uses the `owned_by_user_id` field (`U01234567890123456789012345`) in the response to query data for the owner, who turns out the be `John Doe`:

=== "Request"

    ```HTTP hl_lines="1"
    GET /users/U01234567890123456789012345 HTTP/1.1
    Host: api.investsuite.com
    Accept: application/json
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    ```

=== "Response (body)"

    ```JSON hl_lines="9 10"
    {
        "readable_by": [
            "U01234567890123456789012345",
        ],
        "modifiable_by": [
            "U01234567890123456789012345",
        ],
        "external_id": "my_external_user_id",
        "first_name": "John",
        "last_name": "Doe",
        "email": "john_doe@example.com",
        ...
        "id": "U01234567890123456789012345",
        ...
    }
    ```

!!! Note
    In this example, unless the client already knows the `UserID` of the owner for this portfolio, the requests will by definition always be sequential because the required path parameter for the requests to `/users/` is not known until the first response is received.

With embedding, both requests can be rolled into a single operation, which adds the `_embedded` key to the root of the response (see below). Note that this object contains a dictionary where the keys are the referenced `EntityID`s and the value is the data for that entity (in this case a user object). The client can now directly parse the information for the `UserID` appearing in the `owned_by_used_id` field from the `_embedded` object without having to perform a subsequent request to `/users/`.

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios/P01234567890123456789012345?embed=owned_by_user_id HTTP/1.1
    Host: api.investsuite.com
    Accept: application/json
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    ```

=== "Response (body)"

    ```JSON hl_lines="10 18"
    {
        "readable_by": [
            "U01234567890123456789012345"
        ],
        "modifiable_by": [
            "U01234567890123456789012345"
        ],
        "external_id": "my_exeternal_portfolio_id",
        "name": "Example Portfolio",
        "owned_by_user_id": "U01234567890123456789012345",
        "portfolio": {
            ...
        },
        ...
        "id": "P01234567890123456789012345",
        ...
        "_embedded": {
            "U01234567890123456789012345": {
                "readable_by": [
                    "U01234567890123456789012345",
                ],
                "modifiable_by": [
                    "U01234567890123456789012345",
                ],
                "external_id": "my_external_user_id",
                "first_name": "John",
                "last_name": "Doe",
                "email": "john_doe@example.com",
                ...
                "id": "U01234567890123456789012345",
                ...
            }
        }
    }
    ```
