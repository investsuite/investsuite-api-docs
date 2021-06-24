---
title: Create and manage portfolios
---

A portfolio holds one or more investment products for instance stocks, ETFs or cryptocurrency. The composition of a portfolio depends on the investor’s goals, budget, risk appetite and the holding period. An investor can without advise decide what instruments a portfolio is made of. In this case it is a “self managed” portfolio. A portfolio can equally be composed by an algorithm such as the InvestSuite’s iVar algorithm. In that case the portfolio is managed by a Robo Advisor.

Once you have created a user you can create a portfolio and assign the user as owner.

## Create a portfolio

=== "Request"

    ```HTTP hl_lines="1"
    POST /portfolios/ HTTP/1.1
    Host: public-api.uat.investsuite.com
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Content-Type: application/json
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...

    {
        "base_currency":"USD",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_settings":{
                "goal_id":"L01EF46X4872VVN0QRW4XF2ZP6W",
                "horizon_id":"H01EQ3429CY6Y2NW0ZF8A8Y2FYJ",
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
                "start_amount":10000,
            },
            "manager_version":1
        },
        "external_id":"your-bank-portfolio-1",
        "money_type":"PAPER_MONEY",
        "name":"General investing",
        "owned_by_user_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "portfolio":{
            "$USD":10000
        },
    }

    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "external_id":"your-bank-portfolio-1",
        "readable_by":[
            "U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
            "U01F5WYKRRXZHXT9S6FF1JZNJVZ"
        ],
        "modifiable_by":[
            "U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
            "U01F5WYKRRXZHXT9S6FF1JZNJVZ"
        ],
        "name":"General investing",
        "owned_by_user_id":"U01F5WYKRRXZHXT9S6FF1JZNJVZ",
        "base_currency":"USD",
        "money_type":"PAPER_MONEY",
        "config":{
            "manager":"ROBO_ADVISOR_DISCRETIONARY",
            "manager_version":1,
            "manager_settings":{
                "policy_id":"Y01EF46X9XB437JS4678X0K529C",
                "goal_id":"L01EF46X4872VVN0QRW4XF2ZP6W",
                "horizon_id":"H01EQ3429CY6Y2NW0ZF8A8Y2FYJ",
                "risk_profile_id":null,
                "divest_amount":null,
                "start_amount":10000,
                "recurring_deposit_amount":null,
                "end_datetime":null,
                "active":true,
                "proposed_risk_profile_id":null,
                "tags":[
                    
                ],
                "risk_profile_score":null,
                "background_image":null,
                "background_color":null
            }
        },
        "brokerage_account":null,
        "portfolio":{
            "$USD":10000
        },
        "snapshot_datetime":null,
        "archived":false,
        "funded_since":null,
        "id":"P01F8ZSNV0J45R9DFZ3D7D8C26F",
        "creation_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version":1,
        "version_datetime":"2021-06-24T19:59:15.474241+00:00",
        "version_authored_by_user_id":"U01EJQSYGYQJJ5GNFM4ZXW59Q0X",
        "deleted":false,
        "status":"WAITING_FOR_FUNDS"
    }
    ```