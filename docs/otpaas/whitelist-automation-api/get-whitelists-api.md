# Get Whitelist of an OTP app API
This API retrieves whitelists (email domain and/or user email) of a OTP app

## Request
```
GET /otp/namespace/{namespace}/apps/{appid}/whitelists
```
## Request Path Parameters
| Name      | Description                                           |
| --------- | ----------------------------------------------------- |
| namespace | The tenant namespace of which the OTP app belongs to. |
| appid     | The app id of your OTP app.                           |

## Request Query Parameters
| Name          | Description                                                                                                                                                                                                                                                                                                     |
| ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| lastKey       | The last whitelist id of the previous request. Should be used when retrieving next page of whitelists.                                                                                                                                                                                                          |
| size          | The maximum number of whitelists returned in a single response. Default is 20. Minimum value is 1. Maximum value is 50.                                                                                                                                                                                                           |
| whitelistType | The type of whitelists returned. Accepts three different values, `"all"`, `"emailDomain"` and `"userEmail"`. Using `"all"` will return whitelists of all types. Using `"emailDomain"` will return only email domain whitelists. Using `"userEmail"` will return only user email whitelists. Default is `"all"`. |

## Request Body Schema
No request body is needed for this API.

## Request Body Fields
No request body is needed for this API.

## Response
The response status would be `200 OK`.

## Response Body Schema

?> Note: Fields with value of `0` denotes those field containing a number type.

```json
{
    "count": 0,
    "lastKey": "string",
    "whitelists": [
        {
          "expiresAt": "string",
          "whitelistId": "string",
          "whitelistValue": "string"
        }
    ]
}
```

## Response Body Properties
| Name           | Description                                                                                                                                                         |
| -------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| count          | The number of whitelists returned in the response.                                                                                                                  |
| lastKey        | The whitelist id of the last whitelist returned in the response. This value is to be used in the `lastKey` query parameter to retrieve the next page of whitelists. |
| whitelists     | Array of whitelists returned.                                                                                                                                       |
| expiresAt      | Expiry datetime of whitelist in RFC3339 format `(YYYY-MM-DDThh:mm:ssZ)`, in UTC timezone.                                                                           |
| whitelistId    | Id of whitelist (UUIDv4 format).                                                                                                                                    |
| whitelistValue | Value of whitelist.                                                                                                                                                 |

## Usage Examples

Example for getting all whitelists of an OTP App

On the first request to retrieve the first page of whitelists, there is no need to specify the `lastKey` query parameter. The `size` query parameter can be specified here.

**Request**
```http
GET https://api.stg.techpass.suite.gov.sg/otp/namespace/example_namespace/apps/example_app_id/whitelists?size=10
```
**Response**
```
200 OK
Content-type: application/json

{
    "count": 10,
    "lastKey": "53d55cef-65bf-480c-87ce-f79f748b8356",
    "whitelists": [
        {
            "whitelistId": "5c17ff9f-e49d-4851-8c99-5ce3484151cf",
            "whitelistValue": "test.com"
        },
        .
        .
        .
    ]
}

```
On subsequent requests, use the value of `lastKey` from the response as the value for the `lastKey` query parameter to get the subsequent pages. Once the value of `lastKey` from the response is an empty string `""`, that denotes the response contains the last page of whitelists. An example of this is shown below.

**Request**
```http
GET https://api.stg.techpass.suite.gov.sg/otp/namespace/example_namespace/apps/example_app_id/whitelists?size=10&lastKey=0f6eaaf5-2993-4a16-ac64-1796a94c1efa
```
**Response**
```
200 OK
Content-type: application/json

{
    "count": 8,
    "lastKey": "",
    "whitelists": [
        {
            "whitelistId": "10d24919-46aa-454c-a47f-57099119390f",
            "whitelistValue": "gov.sg"
        },
        .
        .
        .
    ]
}

```

## Error Responses

Known error response include:
- `404 Not Found` if the otp app id supplied is non existent.
- `400 Bad Request` if the query parameters are not of correct formats

An example of this is shown below.

**Errorneous Request**
```http
GET https://api.stg.techpass.suite.gov.sg/otp/namespace/example_namespace/apps/non_existent_appid/whitelists
```
**Errorneous Response**
```http
404 Not Found
Content-type: application/json

{
    "error": {
        "code": 14014,
        "message": "otp app id not found",
        "traceId": "283387d2a40757de"
    }
}
```