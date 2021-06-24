---
title: Opening an investment account
---

To offer to your clients InvestSuite products you open an account on behalf of them, either by passing the data you request via an app or web site, or by sending the data stored in your banking system or CRM. 

The data that are required to open an account are two-fold. Firstly an identifier to reference the user, and secondly a Portfolio object assigned to the user.

## Create and manage users

Users exist for two purposes. Users are granted access to one or more InvestSuite products i.e. StoryTeller, Robo Advisor, Self Investor or the Admin Console. And users can be owners of one or more portfolios. To create a user a limited number of fields are required.

InvestSuite does not require you to store personal data (PII) on its servers. Instead we only require an identifier to reference your client. On top of that the user's e-mail address and phone number are required in case you intend to integrate our front-end experience as well.

**Endpoint**

```
POST /users
```

**Parameters**

Field name | Description | Required
--- | --- | ---
`external_id` | A unique external identifier for this entity, also referred to as Reference ID. This identifier can be any string used in the client's system to identify this entity. Maximum length is 64 characters. This field is not checked for uniqueness. | 
`email` | The email address of the user. This is required if there is a front-end e.g. the Self Investor app or the Admin Console against which the user must be able to log in. This field is checked for uniqueness. The e-mail address is stored in the Identity Platform assigned by the bank. | 
`phone` | The mobile phone number of the user in international format. Same as email this is required for logging in to the front-end, phone number is checked for uniqueness, stored in the IdP of your choice. | 
`first_name` | The user's first name. | 
`last_name` | The user's last name. | 
`broker_id` | The unique client indentifier that is known at the broker for this user. A broker client can own multiple securities accounts, each of which corresponds to an individual portfolio in InvestSuite's system. | 
`idp_id` | The User ID at the identity provider. | 
`language` | `Default: "en-US"` The preferred communication language. | 

**Example request**

```bash
curl -X POST \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer [access_token]"
    -d '{"external_id":"id_123"}'\
    https://{domain}/users/
```

> Learn how to get user data, patch users and delete users from the [API reference documentation](https://public-api.dev.investsuite.com/redoc).

## Create and manage portfolios

A portfolio holds one or more investment products for instance stocks, ETFs or cryptocurrency. The composition of a portfolio depends on the investor’s goals, budget, risk appetite and the holding period. An investor can without advise decide what instruments a portfolio is made of. In this case it is a “self managed” portfolio. A portfolio can equally be composed by an algorithm such as the InvestSuite’s iVar algorithm which underpins a portfolio construction framework. In that case the portfolio is managed by a Robo Advisor.

Once you have created a user you can create a portfolio and assign the user as owner.

**Endpoint**

```
POST /portfolios
```

**Parameters**

Below is a subset of the Portfolio object fields to get you started. The full overview is available in the [API reference documentation](https://public-api.dev.investsuite.com/redoc).

Field name | Description | Required
--- | --- | ---
`external_id` | A unique external identifier for this entity, also referred to as Reference ID. This identifier can be any string used in the client's system to identify this entity. Maximum length is 64 characters. This field is not checked for uniqueness. | 
`name` | The display name of the portfolio set by the user. | ✔️
`owned_by_user_id` | The portfolio owner's user ID. The user owns the portfolio in name only. Cash withdrawals are transferred to the account of the owner. | ✔️ 
`money_type` | `Enum: "REAL_MONEY" "PAPER_MONEY"` Defines whether this is a 'virtual' portfolio or not. Virtual portfolios hold so-called paper money. In a virtual portfolio buying and selling decisions are simulated, rather than placed as actual orders through a broker. | ✔️
`manager` | Default: `"ROBO_ADVISOR_DISCRETIONARY"`, Enum: `"USER_MANAGED" "ROBO_ADVISOR_ADVISORY" "ROBO_ADVISOR_DISCRETIONARY"` Type of the portfolio's manager, which is either: self-managed, managed by an advisor, or discretionary through the Robo Advisor. | ✔️
`manager_version` | Which major version of the selected portfolio manager to use. | ✔️
`policy_id` | The ID of the policy applied in the optimization of this portfolio. Retrieve the policies collection via `GET /policies`. Policies reflect the bank's investment strategies and serve as input for the optimizer.  | ✔️
`goal_id` | The ID of the goal associated with this portfolio. | 
`horizon_id` | The ID of the horizon associated with this portfolio. | 
`proposed_risk_profile_id` | The risk profile proposed for this portfolio. | 
`risk_profile_score` | The risk profile score as computed by the risk profile algorithm. | 


**Example request body**

```json
{
  "external_id": "unique_external_entity_id",
  "name": "General Investing",
  "owned_by_user_id": "U01ARZ3NDEKTSV4RRFFQ69G5FAV",
  "money_type": "REAL_MONEY",
  "config": {
    "manager": "ROBO_ADVISOR_DISCRETIONARY",
    "manager_version": 3,
    "manager_settings": [
      { "policy_id": "78d04cd8ca003cc0041c870a632b657e" }
    ]
  }
}
```

**Example request**

```bash
curl -X POST \
    -H "Content-Type: application/json" \
    -H "Authorization: Bearer [access_token]"
    -d '{"external_id": "unique_external_entity_id","name": "General Investing","money_type": "PAPER_MONEY","config": { "manager_settings": [{ "policy_id": "78d04cd8ca003cc0041c870a632b657e" }]}}'\
    https://{domain}/portfolios/
```