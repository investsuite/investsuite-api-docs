---
title: Retrieve instrument groups
---

## Thematic search

The InvestSuite quant team composes and actively manages a list of themes (instrument groups) such as Healthcare, Clean & Green economy, Social trends each holding an applicable list of financial instruments. The aim of thematic search is to offer in the context of self execution, complementary to other search mechanisms, to your clients a means to filter the stock universe to their personal preference and view. To retrieve themes and selected instruments within them perform a `GET` request against the  `instrument_groups` endpoint.

=== "Request"

    ```HTTP hl_lines="1"
    GET /instrument_groups HTTP/1.1
        ?[limit=100]
        &[embed=instrument_ids]
    Host: public-api.uat.investsuite.com
    Accept: application/json
    Accept-Encoding: gzip, deflate
    Connection: Keep-Alive
    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJ...
    ```

=== "Response (body)"

    ```JSON hl_lines="10"
    {
        "next": null,
        "offset": null,
        "results": [
            {
                "external_id": "unique_external_entity_id",
                "readable_by": [
                    "UXXXXXXXXXXXXXXXXXXXXXXXXXX"
                ],
                "modifiable_by": [
                    "UXXXXXXXXXXXXXXXXXXXXXXXXXX"
                ],
                "id": "J01F3MQ2DPYXPS7JTHGXX4D09D3",
                "creation_datetime": "2021-04-19T00:00:00+00:00",
                "version": 1,
                "version_datetime": "2021-04-19T09:23:34.238210+00:00",
                "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
                "deleted": false,
                "_embedded": {},
                "name": {
                    "en-US": "Cloud Computing"
                },
                "category": {
                    "en-US": "Digital Disruption"
                },
                "image_uri": "urn:ietf:rfc:2648",
                "description": {
                    "en-US": "Cloud Computing is the on-demand availability of computer system resources, especially data storage (cloud storage) and computing power."
                },
                "instrument_ids": [
                    "I01F3MPZ53PNXBQVTKZNKJ88RA3"
                ]
            }
        ],
        "_embedded": {
            "I01F3MPZ53PNXBQVTKZNKJ88RA3": {
                "external_id": "US0378331005",
                "readable_by": [
                    "UXXXXXXXXXXXXXXXXXXXXXXXXXX"
                ],
                "modifiable_by": [
                    "UXXXXXXXXXXXXXXXXXXXXXXXXXX"
                ],
                "name": "Appel BV. Ordinary Shares",
                "display_name": "Appel BV",
                "tags": [],
                "type": "STOCK",
                "image_uri": null,
                "id": "I01F3MPZ53PNXBQVTKZNKJ88RA3",
                "creation_datetime": "2021-04-19T00:00:00+00:00",
                "version": 1,
                "version_datetime": "2021-04-19T09:21:47.127958+00:00",
                "version_authored_by_user_id": "UXXXXXXXXXXXXXXXXXXXXXXXXXX",
                "deleted": false,
                "_embedded": {}
            }
        }
    }
    ```

## Embedding

You can add details about the `Instrument` objects to the `InstrumentGroup` objects collection by providing the `?embed=query` query string parameter. This parameter can be set to one or more field names that are available in the response object (and are references to other entities). The response will be extended with a populated _embedded key at the root level, which contains a full rendition of the referenced entities.

## Collections

Collections are paginated lists of entities. To request a collection issue a `GET` request against the entity root path e.g. `GET /users`, `GET /portfolios`, `GET /instrument_groups`. Collection endpoints accept alongside the `embed` query parameter take two parameters: limit and offset. Limit (`limit=50`) allows you to pass in the number of items to be returned in the results array of the response. The default collection response size is `20 items`. The maximum size is 100. 