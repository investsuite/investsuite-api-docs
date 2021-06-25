---
title: Create and manage users
---

## Account opening

To offer access to InvestSuite products you open an account on behalf of your clients. Opening an account requires passing the data you request via an app or web site, or sending the data stored in your banking system or CRM. The data that are required are two-fold. Firstly an identifier to reference the user, and secondly a Portfolio object assigned to the user.

User objects serve two purposes. Firstly to grant access to one or more InvestSuite products i.e. StoryTeller, Robo Advisor, Self Investor or the Admin Console. Secondly to assign users as owners to one or more portfolios. To create a user a limited number of fields are required.

InvestSuite does not require you to store personal data (PII) on its servers. Instead we demand an identifier to reference your client. On top of that the user's e-mail address and phone number are required in case you intend to integrate our front-end experience.

## Create a user

=== "Request"

    ```HTTP hl_lines="1"
    POST /users/ HTTP/1.1
    Host: public-api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...

    {
        "external_id": "your-bank-user-1",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "brokerage_user_id": "12345678",
        "idp_user_id": "user_idp_identity",
        "language": "en-US"
    }

    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id": "unique_external_entity_id",
        "readable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "modifiable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "_embedded": {},
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "brokerage_user_id": "12345678",
        "idp_user_id": "user_idp_identity",
        "language": "en-US"
    }
    ```

## Get a user

Add the InvestSuite ID to the path to retrieve a user object.

=== "Request"

    ```HTTP hl_lines="1"
    GET /users/U01F8YW5NJXMF78PMFKXTE2R7Q7 HTTP/1.1
    Host: public-api.uat.investsuite.com
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id": "unique_external_entity_id",
        "readable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "modifiable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "_embedded": {},
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "brokerage_user_id": "12345678",
        "idp_user_id": "user_idp_identity",
        "language": "en-US"
    }
    ```

## Search

You can query each entity through a general endpoint e.g. `GET portfolios/?query=…`. Learn more in the [Handling collection responses](/advanced_topics/collections/) section.

=== "Request"

    ```HTTP hl_lines="1"
    GET /users
        ?query=email+eq+'jane*' HTTP/1.1
    Host: public-api.uat.investsuite.com
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id": "unique_external_entity_id",
        "readable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "modifiable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "_embedded": {},
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "brokerage_user_id": "12345678",
        "idp_user_id": "user_idp_identity",
        "language": "en-US"
    }
    ```

## Update a user object

Given the right permissions you can update any object by issuing a `PATCH` request.

=== "Request"

    ```HTTP hl_lines="1"
    PATCH /users/U01F8YW5NJXMF78PMFKXTE2R7Q7 HTTP/1.1
    Host: public-api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...

    {
        "language": "be-NL"
    }

    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id": "unique_external_entity_id",
        "readable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "modifiable_by": [
            "U01ARZ3NDEKTSV4RRFFQ69G5FAV"
        ],
        "id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "creation_datetime": "2021-02-18T08:21:02+00:00",
        "version": 3,
        "version_datetime": "2021-02-18T08:21:02+00:00",
        "version_authored_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
        "deleted": false,
        "_embedded": {},
        "first_name": "Jane",
        "last_name": "Doe",
        "email": "jane.doe@example.com",
        "phone": "+123456789",
        "brokerage_user_id": "12345678",
        "idp_user_id": "user_idp_identity",
        "language": "en-US"
    }
    ```

## Delete a user

Given the right permissions you can delete any object by issuing a `DELETE` request.

=== "Request"

    ```HTTP hl_lines="1"
    DELETE /users/U01F8YW5NJXMF78PMFKXTE2R7Q7 HTTP/1.1
    Host: public-api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "msg":"Resource 'U01F8YW5NJXMF78PMFKXTE2R7Q7' has been deleted"
    }
    ```
