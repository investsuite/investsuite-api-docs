---
title: Embedding Additional Entities in API Responses
---

## Introduction

The InvestSuite API supports optional embedding of entities by `EntityID`, a feature which is modeled after[^1] [Zalando's RESTful API Performance Guideline #158](https://opensource.zalando.com/restful-api-guidelines/#158). Embedding enables API clients to merge referenced entities directly into a single response, which reduces the amount of (sequential) round trips required to fetch data from InvestSuite's backend. This feature is particularly convenient in use cases where data from multiple entities is joined together, a common operation when preparing data for reporting purposes or in specific front end views.

[^1]: The Zalando guidelines include an example of embedding a _sub-resource_ into the response. The InvestSuite API supports embedding of any referenced entity (which does not necessarily have to be a sub-resource of the requested entity). In addition, it is also possible to embed a list of referenced entities instead of a just single instance.

In order to embed certain field(s), the `?embed=` query string parameter needs to be provided in `GET` requests. This parameter can be set to one or more field names that are available in the response object (and are references to other entities). The response will be extended with a populated `_embedded` key at the root level, which contains a full rendition of the referenced entities. This provides a useful level of flexibility in API responses, allowing clients to directly retrieve related data with a single request.

!!! Hint
    Embedding works for any response field value that is an (array of) `EntityID`s, which can be recognized by the globally-unique identifier that follows below regex (such as a `UserID` like `U01234567890123456789012345`). This regex translates to a single alphabet letter identifying the type of entity, followed by a 26-character ULID based on the Crockford base32 character set.

    ```
    ^[A-Z][0-9A-HJKMNP-TV-Z]{26}$
    ```


## Single embedded entity

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
        "external_id": "my_external_portfolio_id",
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

And then uses the `owned_by_user_id` field value (`U01234567890123456789012345`) in the response to query data for the owner, who turns out the be `John Doe`:

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

With embedding, both requests can be rolled into a **single operation**, which adds the referenced entities to the `_embedded` key at the root of the response (see below). Note that this object contains a dictionary where the keys are the referenced `EntityID`s and the value is the data for that entity (in this case a `User` object). The client can now directly parse the information for the `UserID` appearing in the `owned_by_used_id` field from the `_embedded` object without having to perform a subsequent request to `/users/`.

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
        "external_id": "my_external_portfolio_id",
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

!!! Info
    The amount of 'work' required to retrieve data can be significantly reduced on both sides if embedding is used cleverly. In the above example, the number of requests required from the client is cut in half, which speeds up the interaction considerably. At the same time, API resources are used efficiently, as per-request overhead can be avoided.


## Multiple embedded entities

For the next example, assume that the client requires a portfolio, the owner of the portfolio, _and_ the user that updated the portfolio most recently. This can be achieved by adding **multiple response fields** to the `embed=` param, separated by commas:

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios/P01234567890123456789012345?embed=owned_by_user_id,version_authored_by_user_id HTTP/1.1
    Host: api.investsuite.com
    Accept: application/json
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    ```

=== "Response (body)"

    ```JSON hl_lines="10 17 19"
    {
        "readable_by": [
            "U01234567890123456789012345"
        ],
        "modifiable_by": [
            "U01234567890123456789012345"
        ],
        "external_id": "my_external_portfolio_id",
        "name": "Example Portfolio",
        "owned_by_user_id": "U01234567890123456789012345",
        "portfolio": {
            ...
        },
        ...
        "id": "P01234567890123456789012345",
        ...
        "version_authored_by_user_id": "U01234567890123456789012345",
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

!!! Note
    In this case, the referenced entity comes up twice: both as the owner of the portfolio and as the author of the most recent version. As a result, the `_embedded` object only contains a single object (no repeated keys). This helps to further reduce repeated queries and unnecessary overhead.


## Embedding with collections

Similar to single-entity responses, collections support embedding as well. If embedding is requested, the `_embedded` object will contain referenced entities for **all objects returned in the collection**. For instance, a client can request a list of 10 portfolios and directly embed the owners of all these portfolios. This is particularly useful when creating front-end views or collecting data for reporting purposes where multiple entities need to be joined together in bulk. This process also reduces overhead on the server side: 1 query to retrieve portfolios, and 1 query to filter for the list of users is a lot more efficient than requesting these entities sequentially from the client side.

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios/?embed=owned_by_user_id&limit=10 HTTP/1.1
    Host: api.investsuite.com
    Accept: application/json
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    ```

=== "Response (body)"

    ```JSON hl_lines="6 11 18 22"
    {
        "next": "http://api.investsuite.com/portfolios/?embed=owned_by_user_id&limit=10&offset=P01234567890123456789012345",
        "offset": "P01234567890123456789012345",
        "results": [
            {
                "owned_by_user_id": "U01234567890123456789012345",
                "id": "P01234567890123456789012345",
                ...
            },
            {
                "owned_by_user_id": "U98765432109876543210987654",
                "id": "P98765432109876543210987654",
                ...
            },
            ...
        ],
        "_embedded": {
            "U01234567890123456789012345": {
                "first_name": "John",
                ...
            },
            "U98765432109876543210987654": {
                "first_name": "Jane",
                ...
            },
            ...
        }
    }
    ```


## Embedding a reference to a list of entities

Finally, it is also possible to embed not just a single referenced entity, but an array pointing at multiple entity IDs. The `readable_by` and `modifiable_by` arrays in the standard entity response are a good example, as these can contain references to multiple (different) entities, such as users and user groups. Suppose the requirement is to retrieve all users that have read access to a certain portfolio, which again can be achieved with a single request. In the below response, user `Jane` has view-only permissions to the portfolio of `John`, who is the owner and has both read and write permissions:

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios/P01234567890123456789012345?embed=readable_by HTTP/1.1
    Host: api.investsuite.com
    Accept: application/json
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    ```

=== "Response (body)"

    ```JSON hl_lines="3 4 18 22"
    {
        "readable_by": [
            "U01234567890123456789012345",
            "U98765432109876543210987654",
        ],
        "modifiable_by": [
            "U01234567890123456789012345"
        ],
        "external_id": "my_external_portfolio_id",
        "name": "Example Portfolio",
        "owned_by_user_id": "U01234567890123456789012345",
        "portfolio": {
            ...
        },
        "id": "P01234567890123456789012345",
        ...
        "_embedded": {
            "U01234567890123456789012345": {
                "first_name": "John",
                ...
            },
            "U98765432109876543210987654": {
                "first_name": "Jane",
                ...
            }
        }
    }
    ```


## FAQ

!!! faq "What common errors can be encountered when working with embedding?"
    The API will throw helpful error messages when the requested embedding could not be completed successfully, indicating the location of the error (`loc`, pointing at `query`, parameter: `embed`), and a human-readable error message with a reference to the received value.

    ```JSON
    {
        "title": "Bad request",
        "detail": [
            {
                "loc": [
                    "query",
                    "embed"
                ],
                "msg": "field 'not_an_existing_field' not found in this entity",
                "type": "value_error.exception"
            }
        ],
        "status": 400
    }
    ```

    In case a field is requested that that cannot be embedded (i.e. a field that is not a reference to another entity), the error message will be:

    ```JSON
    "msg": "field 'first_name' cannot be embedded"
    ```

!!! faq "What happens if the requesting user does not have access to the embedded entity?"
    Read and write permissions are controlled by the `readable_by` and `modifiable_by` arrays on each entity respectively. If the requesting user does not have the right permissions (in this case, its ID is not in the `readable_by` list on the referenced entity), a standard `403 Forbidden`. Note that the error references the _embedded entity_, which is another user in the below example. This could be slightly confusing when the original request was for a different entity such as a portfolio, but the error indicates lack of read access to a user.

    ```JSON
    {
        "title": "Access forbidden",
        "detail": "User U01234567890123456789012345 does not have READ access on U98765432109876543210987654 (version=latest)",
        "status": 403
    }
    ```

!!! faq "What happens if a reference to a deleted entity is embedded?"
    Similar to the permission error above, when a reference to an entity is requested for embedding, but that entity is no longer available, a standard `404 Not Found` response is returned. Again, bear in mind that this error is specific to the *embedded entity*. It might for instance be possible to retrieve a portfolio instance, but if an embedded reference to the owner of that portfolio is a deleted object, the error will indicate this:

    ```JSON
    {
        "title": "Resource not found",
        "detail": "Entity 'U01234567890123456789012345' (version=0) not found for tenant 'example.com'",
        "status": 404,
    }
    ```