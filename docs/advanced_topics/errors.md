---
title: A closer look at API errors
---

Errors happen. If that is the case the API returns a `4**` error if it assumes that the error is caused by your application, or a `5**` in case of a server error. If there is no error anticipated the API returns a 2** code.

### Error codes

Code | Status | Description
--- | --- | ---
200 | OK | The request was successfully completed.
201 | Created | A new resource was successfully created.
400 | Bad Request | The request was invalid.
401 | Unauthorized | The request did not include an authentication token or the authentication token was expired.
403 | Forbidden | The client did not have permission to access the requested resource.
404 | Not Found | The requested resource was not found.
422 | Validation error | The request was understood but a validation error prevented the server to process the request. If this is the case the API adds context to the response body (see example below).
500 | Internal Server Error | The request was not completed due to an internal error on the server side.


### Example of a 422 response body

When a validation error occurs the reason and the location are documented in the response body.

```json
{
"title":"Bad request",
"detail":[
    {
        "loc”: [“body","entity","base_currency"],
        "msg":"base_currency may not be updated because it is marked with allow_mutation=False"
    }
  ]
}
```