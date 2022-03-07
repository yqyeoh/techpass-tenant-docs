# Add Whitelists API
This API allows the adding of a batch of email domains and/or user email for whitelisting per request.

?> Note: This API only adds whitelists to a single OTP app.

## Request
```
POST /otp/namespace/{namespace}/apps/{appid}/whitelists
```
## Request Path Parameters
| Name      | Description                                           |
| --------- | ----------------------------------------------------- |
| namespace | The tenant namespace of which the OTP app belongs to. |
| appid     | The app id of your OTP app.                           |

## Request Query Parameters
No query parameters for this API

## Request Body Schema
```json
{
    "emailDomains": [
        {
            "emailDomain": "string",
			"expiresAt": "string"
        },
    ],
    "userEmails": [
        {
            "userEmail": "string",
			"expiresAt": "string"
        },
    ]
}
```
## Request Body Fields
| Name         | Description                                                                                                                                                                                              |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| emailDomains | Array of email domains to add. Field should be left out if not adding any email domains for whitelisting.                                                                                                |
| userEmails   | Array of user emails to add. Field should be left out if not adding any user emails for whitelisting.                                                                                                    |
| emailDomain  | Email domain to whitelist. For e.g, `"tech.gov.sg"`.                                                                                                                                                          |
| userEmail    | User email to whitelist. For e.g, `"john@tech.gov.sg"` .                                                                                                                                                 |
| expiresAt    | Expiry datetime of whitelist. Must be a future datetime in RFC3339 format `(YYYY-MM-DDThh:mm:ssZ)`. For e.g, `2050-02-23T16:00:00Z`. Use UTC timezone. Field should be left out if not adding any expiration to whitelist. |

!> Note: The sum of the number of email domains and user emails to be whitelisted cannot exceed 1000.

## Response

The response status would either be `207 Multi Status` or `400 Bad Request`. If the request itself is malformed, for e.g: having invalid email domain/user email/expiration, `400 Bad Request` would be returned. If the request itself is not malformed, then `207 Multi Status` would be returned. **However, this does not mean that all email domain/user email entries are whitelisted.** Please check for the presence of the `errors` key in the response body (more information below).


## Response Body Schema

?> Note: Fields with value of `0` denotes those field containing a number type.

```json
{
    "emailDomains": {
        "errors": [
            {
                "code": 0,
                "entryNumber": 0,
                "message": "string",
                "status": 0,
                "traceId": "string"
            }
        ],
        "whitelisted": [
            {
                "expiresAt": "string",
                "whitelistId": "string",
                "whitelistValue": "string"
            }
        ]
    },
    "userEmails": {
        "errors": [
            {
                "code": 0,
                "entryNumber": 0,
                "message": "string",
                "status": 0,
                "traceId": "string"
            }
        ],
        "whitelisted": [
            {
                "expiresAt": "string",
                "whitelistId": "string",
                "whitelistValue": "string"
            }
        ]
    }
}
```

## Response Body Properties
| Name           | Description                                                                                                                                                             |
| -------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| emailDomains   | Array of whitelisted email domains. Field would not be present if no email domains were in request to be whitelisted.                                                   |
| userEmails     | Array of whitelisted user emails. Field would not be present if no user emails were in request to be whitelisted.                                                       |
| whitelisted    | Array of whitelisted email domains / user emails. Field would not be present if no email domain/user emails successfully whitelisted.                                   |
| whitelistValue | Whitelisted email domain / user email.                                                                                                                                  |
| whitelistId    | Unique identifier (UUIDv4) of the whitelisted email domain / user email.                                                                                                |
| expiresAt      | Expiry datetime of a whitelist in RFC3339 format `(YYYY-MM-DDThh:mm:ssZ)`. Field would not be present if whitelist has no expiry.                                       |
| errors         | Array of errors, one for each erroneous whitelist entry (email domain / user email in request). Field would not be present if there are no erroneous whitelist entries. |
| code           | Error code                                                                                                                                                              |
| entryNumber    | Index of the email domain / user email entry in the request that is erroneous.                                                                                          |
| message        | Error message describing the error of the erroneous email domain / user email entry.                                                                                    |
| status         | Status code of the erroneous email domain / user email entry. E.g, `409 Conflict`                                                                                       |
| traceId        | If any error persists and you require support, please file a support ticket [here](https://go.gov.sg/techpass-sr) and attach the traceId.                               |

## Usage Examples

?> Refer to the [Endpoints](/otpaas/whitelist-automation-api/intro?id=endpoints) section to view the staging and production Automation API endpoints.

Example for adding only email domains

**Request**
```http
POST https://{automation_api_endpoint}/otp/namespace/example_namespace/apps/example_app_id/whitelists
Content-type: application/json

{
    "emailDomains": [
        {
            "emailDomain": "tech.gov.sg"
        },
        {
            "emailDomain": "moh.gov.sg"
        }
    ],
}
```
**Response**
```
207 Multi Status
Content-type: application/json

{
    "emailDomains": {
        "whitelisted": [
            {
                "whitelistId": "fa8e4444-1d36-45fd-985a-699491103043",
                "whitelistValue": "tech.gov.sg"
            },
            {
                "whitelistId": "8adb86f1-ba7f-4a91-a38d-11be70183fec",
                "whitelistValue": "moh.gov.sg"
            }
        ]
    },
}
```


Example for adding only user emails

**Request**
```http
POST https://{automation_api_endpoint}/otp/namespace/example_namespace/apps/example_app_id/whitelists
Content-type: application/json

{
    "userEmails": [
        {
            "userEmail": "user_a@tech.gov.sg"
        },
        {
            "userEmail": "user_b@tech.gov.sg"
        }
    ],
}
```
**Response**
```
207 Multi Status
Content-type: application/json

{
    "userEmails": {
        "whitelisted": [
            {
                "whitelistId": "c203795d-316e-46f6-8853-625c2a4bd1dd",
                "whitelistValue": "user_a@tech.gov.sg"
            },
            {
                "whitelistId": "2f2c81dd-4c1f-475a-b035-5311c409c920",
                "whitelistValue": "user_b@tech.gov.sg"
            }
        ]
    },
}
```

Example for adding both email domains and user emails

**Request**
```http
POST https://{automation_api_endpoint}/otp/namespace/example_namespace/apps/example_app_id/whitelists
Content-type: application/json

{
    "emailDomains": [
        {
            "emailDomain": "moe.gov.sg"
        },
        {
            "emailDomain": "mom.gov.sg"
        }
    ],
    "userEmails": [
        {
            "userEmail": "user_c@tech.gov.sg"
        },
        {
            "userEmail": "user_d@tech.gov.sg"
        }
    ],
}
```
**Response**
```
207 Multi Status
Content-type: application/json

{
    "emailDomains": {
        "whitelisted": [
            {
                "whitelistId": "b380ae78-6a61-4000-bdd2-518e24fd3852",
                "whitelistValue": "moe.gov.sg"
            },
            {
                "whitelistId": "bb33c2e8-0e9d-43ab-b68e-89fd875a1672",
                "whitelistValue": "mom.gov.sg"
            }
        ]
    },
    "userEmails": {
        "whitelisted": [
            {
                "whitelistId": "8c3ce17d-9a08-4f70-9b55-6645a8377424",
                "whitelistValue": "user_c@tech.gov.sg"
            },
            {
                "whitelistId": "eacbd89b-85a1-43fe-853f-3beec5fbbbc9",
                "whitelistValue": "user_d@tech.gov.sg"
            }
        ]
    },
}
```

Example for adding email domains with expiry

**Request**
```http
POST https://{automation_api_endpoint}/otp/namespace/example_namespace/apps/example_app_id/whitelists
Content-type: application/json

{
    "emailDomains": [
        {
            "emailDomain": "mot.gov.sg"
            "expiresAt": "2023-03-01T04:41:57Z"
        },
        {
            "emailDomain": "mom.gov.sg"
            "expiresAt": "2023-04-12T10:15:25Z"
        }
    ],
}
```
**Response**
```
207 Multi Status
Content-type: application/json

{
    "emailDomains": {
        "whitelisted": [
            {
                "whitelistId": "1a144f46-b23d-4ec1-b9f8-6c59d098d781",
                "whitelistValue": "mot.gov.sg"
                "expiresAt": "2023-03-01T04:41:57Z"
            },
            {
                "whitelistId": "96df0326-60e3-4dbc-8bc5-df69e52366d5",
                "whitelistValue": "mom.gov.sg"
                "expiresAt": "2023-04-12T10:15:25Z"
            }
        ]
    },
}
```
## Error Responses

There are two types of error responses. The first type is when a `400 Bad Request` response is returned. The second type is when a `207 Multi Status` response is returned but the response body contains the `"errors"` key.

The first error response is only returned when any of the values given is in an invalid format, such as invalid email domain, user email and/or expiry.
An example of such an error response is shown below:

**Errorneous Request**
```http
POST https://{automation_api_endpoint}/otp/namespace/example_namespace/apps/example_app_id/whitelists
Content-type: application/json

{
    "emailDomains": [
        {
            "emailDomain": "moegovsg"
        },
    ]
}

```
**Errorneous Response**
```http
400 Bad Request
Content-type: application/json

{
    "error": {
        "code": 400,
        "message": "emailDomains[0]: the value of 'emailDomain' does not match any of the following pattern(s): \n'domain' pattern: ^([a-z0-9]([a-z0-9-]{0,61}[a-z0-9])?\\.)+[a-z0-9][a-z0-9-]{0,61}[a-z0-9]$ ",
        "traceId": ""
    }
}
```

The second error response is returned if the values within the request are in valid format but still errorneous. Examples of this include email domains/user emails being already whitelisted, expiration given being in the past, etc. Thus, this error response is not really an error response, more like a normal response but with the `"errors"` key to denote that the values in the request were errorneous.

An example of such an error response is shown below:

**Errorneous Request**
```http
POST https://{automation_api_endpoint}/otp/namespace/example_namespace/apps/example_app_id/whitelists
Content-type: application/json

{
    "emailDomains": [
        {
            "emailDomain": "alreadywhitelisted.com"
        },
    ]
}

```
**Errorneous Response**
```http
207 Multi Status
Content-type: application/json

{
    "emailDomains": {
        "errors": [
            {
                "code": 14010,
                "entryNumber": 0,
                "message": "entry is already whitelisted",
                "status": 409,
                "traceId": "8ae0bc023b97bd3"
            },
        ]
    }
}
```
Note that for the second type of error response, each error in `"errors"` would correspond to a whitelist entry that is erroneous. The `"entryNumber"` key is the index of the whitelist entry in the request that is errorneous, hence would allow you to know which exact entry is erroneous.

Due to the two types of error responses, the recommended way to handle errors for this API is to check for the first type of error response. Subsequently, if there are no error response, check if the `207 Multi Status` response contains the `"errors"` key. If the response does not contain the `"errors"` key, then there are truly no errors and all email domains and/or user emails in request should be successfully whitelisted.
