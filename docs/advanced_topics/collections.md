---
title: Handling collection responses
---

At this point, you have moved beyond the common scenarios and are ready to learn more about the inner workings of the InvestSuite API. Most of the topics allow you to run scenarios or to try out yourselves. Such requires authentication data. Reach out to your representative and we will provide you with credentials in no time. Find out how to authenticate in the [Get started](../first_steps.md) chapter.

## Collection response format

Collections are paginated lists of entities. To request a collection issue a `GET` request against the entity root path e.g. `GET /users`, `GET /portfolios`, `GET /instrument_groups`.

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios/
        ?embed=owned_by_user_id
        &limit=2 HTTP/1.1
    Host: public-api.uat.investsuite.com
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "next":"http://public-api.uat.investsuite.com/portfolios/?embed=owned_by_user_id&limit=2",
        "count":204,
        "offset":"10",
        "results":[
        {
            "external_id":"AE820230000000000001918",
            "readable_by":[
                "U01ERYVVSZTKKYEEVX3Y6JECE83"
            ],
            "modifiable_by":[
                "U01ERYVVSZTKKYEEVX3Y6JECE83"
            ],
            "name":"Child's education",
            "owned_by_user_id":"U01ERYVVSZTKKYEEVX3Y6JECE83",
            "base_currency":"USD",
            "money_type":"REAL_MONEY",
            "config":{
                "manager":"ROBO_ADVISOR_DISCRETIONARY",
                "manager_version":1,
                "manager_settings":{
                    "policy_id":"Y01F5G34T4FE6T1EH798FA0M170",
                    "goal_id":"L01EF46X3JH1QDFPD270BKSDTTX",
                    "horizon_id":"H01EF46X564YMWFR8GTNBED16CN",
                    "risk_profile_id":"K01EF46X76V5VB4E92FC1D01856",
                    "divest_amount":null,
                    "start_amount":600.0,
                    "recurring_deposit_amount":500.0,
                    "end_datetime":null,
                    "active":true,
                    "proposed_risk_profile_id":"K01EF46X76V5VB4E92FC1D01856",
                    "tags":[
                    
                    ],
                    "risk_profile_score":3,
                    "background_image":"https://images.unsplash.com/photo-1526304640581-d334cdbbf45e?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1200&q=82",
                    "background_color":null
                }
            },
            "brokerage_account":{
                "brokerage_portfolio_id":"BrokerAccountKey",
                "brokerage_user_id":"BrokerAccountId",
                "bank_account_type":"IBAN",
                "bank_account_number":"AE820230000000000001918",
                "bank_id":null
            },
            "owned_by_user_ids":[
                
            ],
            "portfolio":{
                
            },
            "snapshot_datetime":null,
            "archived":false,
            "funded_since":null,
            "id":"P01F7KDVY6H718JNV91840DE5V4",
            "creation_datetime":"2021-06-07T00:00:00+00:00",
            "version":2,
            "version_datetime":"2021-06-07T14:26:21.231855+00:00",
            "version_authored_by_user_id":"U01ERYVVSZTKKYEEVX3Y6JECE83",
            "deleted":false,
            "_embedded":{
                
            },
            "status":"WAITING_FOR_FUNDS"
        }
        ],
        "_embedded":{
        "U01ERYVVSZTKKYEEVX3Y6JECE83":{
            "external_id":null,
            "readable_by":[
                "U01F5XFY0S38KJYVHX0X3GJ4KWM",
                "UXXXXXXXXXXXXXXXXXXXXXXXXXX"
            ],
            "modifiable_by":[
                "U01F5XFY0S38KJYVHX0X3GJ4KWM",
                "UXXXXXXXXXXXXXXXXXXXXXXXXXX"
            ],
            "id":"U01F5XFY0S38KJYVHX0X3GJ4KWM",
            "creation_datetime":"2021-05-17T00:00:00+00:00",
            "version":1,
            "version_datetime":"2021-05-17T15:43:23.427412+00:00",
            "version_authored_by_user_id":"UXXXXXXXXXXXXXXXXXXXXXXXXXX",
            "deleted":false,
            "_embedded":{
                
            },
            "first_name":"Laurent",
            "last_name":"Sorber",
            "email":"laurent.sorber@gmail.com",
            "phone":null,
            "brokerage_user_id":null,
            "idp_user_id":"a8c8b808-bad9-4acb-babf-aae52488e468",
            "language":"en-US",
            "products":[
                
            ]
        }
        }
    }    
    ```

## Query parameters

Collection endpoints accept alongside the `embed` query parameter two parameters: limit and offset. 

Parameter | Description
--------- | -----------
limit     | Example: `limit=50`) Aallows you to pass in the number of items to be returned in the results array of the response. The default collection response size is `20 items`. The maximum size is 100.
offset    |  Example `offset=50` To be used in combination with the `limit` parameter, the offset defines the number of records that need to be skipped.
embed     | Example: `embed=field_name_1,field_name_2` Optional comma-separated list of field names for which the referenced entity is to be included in the _embedded object of the response. See [Embedding](embedding.md).
search    | Example: `query=email+eq+'jane.doe@example.com` See below

## Search

The API provides a structural search and filtering mechanism for all entities. We already opted to work with a query string parameter `query=email+eq+'jane.doe@example.com` instead of a POST body for more efficient client-side caching and ease of use for testing. The query syntax is a mix of both [OData](https://www.odata.org/) and [Apache Lucene Query Parser Syntax](https://lucene.apache.org/core/2_9_4/queryparsersyntax.html).

=== "Request"

    ```HTTP hl_lines="1"
    GET /portfolios
        ?query=lastmodified+in+['20200101'+to+'20240101'] HTTP/1.1
    Host: public-api.uat.investsuite.com
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...
    ```

### Fundamental rules

- Each entity can be queried by issuing a `GET` request e.g. `GET users/?query=email+eq+'jane*'`.
- Spaces are substituted with a plus sign ( + ) because space is not a valid character in an URL.
- String and date literals are always enclosed in single-quotes. 
- Spaces in a string literal are also denoted with a plus sign ( + ).
- If you need the plus sign in a string literal, just escape it with a backslash like email+eq+'*luk\\+*'.

### Operators

Number Operators

- Exact match (e.g. `age+eq+1`)
- LT (less than) (e.g. `age+lt+18`)
- LTE (less than or equal) (e.g. `age+le+18`)
- GT (greater than) (e.g. `age+gt+18`)
- GTE (greater than or equal) (e.g. `age+ge+18`)

String Operators

- Exact match (e.g. email+eq+'kristof.leroux@investsuite.com')
- Prefix search (starts with) (e.g. email+eq+kristof*')
- Postfix search (ends with)  (e.g. email+eq+'*gmail.com')
- LIKE matching (contains) (e.g. email+eq+'*gmail*')

Date Operators

- Exact match (e.g. `last_modified+eq+'2020-09-11T09:03:53.721588+00:00'`) (with or without time, with or without timezone)
- RANGE (is in range) for dates 
    - without time (e.g. `lastmodified+in+['20200101'+to+'20210101']`)
    - with time (e.g. `lastmodified+in+['2020-09-11T09:03:53.721588'+to+'2020-11-11T09:03:53.721588']`)
    - with timezone (e.g. `lastmodified+in+['2020-09-11T09:03:53.721588+00:00'+to+'2020-11-11T09:03:53.721588+00:00']`)

Boolean Operators

- Boolean Queries (e.g. `archived+eq+true`)

List Operators

- IN (element of) (e.g `field+in+['value1',+'value2',+'value3']`)

Logical Operators:

- AND (e.g. `email+eq+kristof+and+age+gt+18`)

!!! Hint
    You combine operators in all possible compositions e.g. `email+eq+'*tom*'+and+firstname+in+[’tom',+‘thomas’]`.

### Sorting

The sorting operator always comes as the last term, except when there is a selection term which is always at the very last:

- orderby {attribute_name}, orderby {attribute_name} asc e.g. `orderby+age` is always ascending, so the same as `orderby+age+asc`
- Order descending: orderby {attribute_name} desc e.g. `orderby+age+desc`
- Multiple attributes: orderby {attribute_name} desc, {attribute_name2} asc e.g. `orderby+age+desc,+firstname`
- Attributes names can be nested e.g. `orderby+manager.bank_account`.