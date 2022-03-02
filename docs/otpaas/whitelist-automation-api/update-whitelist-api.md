# Update Whitelist API
This API allows the updating of a single of email domains / user email whitelist of a OTP app

## Request
```
PUT /otp/namespace/{namespace}/apps/{appid}/whitelists/{whitelistid}
```
## Request Path Parameters
| Name        | Description                                           |
| ----------- | ----------------------------------------------------- |
| namespace   | The tenant namespace of which the OTP app belongs to. |
| appid       | The app id of your OTP app.                           |
| whitelistid | The id of the whitelist to update                     |

## Request Query Parameters
No query parameters for this API

## Request Body Schema
```json
{
    "expiresAt": "string",
    "whitelistValue": "string"
}
```
## Request Body Fields
| Name         | Description                                                                                                                                                                                                              |
| ------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| whitelistValue | New value of whitelist. Field should be left out if the whitelist value is not being updated. |
| expiresAt    | Expiry datetime of whitelist. Must be a future datetime in RFC3339 format `(YYYY-MM-DDThh:mm:ssZ)`. For e.g, `2050-02-23T16:00:00Z`. Use UTC timezone. Field should be left out if not updating expiration of whitelist. **Note: To remove expiration from whitelist, put empty string `""` for this field.** |

**Note: A whitelist cannot be updated to be of another type, thus a email domain whitelist cannot be updated to be a user email whitelist and vice-versa.**

## Response

The response status would be `204 No Content`.


## Response Body Schema
No response body is returned for this API.

## Response Body Properties
No response body is returned for this API.

## Usage Examples

Example for setting an expiration of a whitelist

**Request**
```http
PUT https://api.stg.techpass.suite.gov.sg/otp/namespace/example_namespace/apps/example_app_id/whitelists/f64a12fb-8952-4b99-80ce-0052d42a3362
Content-type: application/json

{
    "expiresAt": "2024-03-02T08:36:07Z"
}
```
**Response**
```
204 No Content
```

Example for removing an expiration of a whitelist

**Request**
```http
PUT https://api.stg.techpass.suite.gov.sg/otp/namespace/example_namespace/apps/example_app_id/whitelists/f64a12fb-8952-4b99-80ce-0052d42a3362
Content-type: application/json

{
    "expiresAt": ""
}
```
**Response**
```
204 No Content
```

Example for updating the whitelist value of an email domain whitelist

**Request**
```http
PUT https://api.stg.techpass.suite.gov.sg/otp/namespace/example_namespace/apps/example_app_id/whitelists/f64a12fb-8952-4b99-80ce-0052d42a3362
Content-type: application/json

{
    "whitelistValue": "tech.gov.sg"
}
```
**Response**
```
204 No Content
```

## Error Responses

Known error response include:
- `404 Not Found` if the otp app id or the whitelist id supplied is non existent.
- `409 Conflict` if whitelist value given is already been whitelisted
- `400 Bad Request` if the expiration given is in the past

An example of this is shown below.

**Errorneous Request**
```http
PUT https://api.stg.techpass.suite.gov.sg/otp/namespace/example_namespace/apps/example_app_id/whitelists/nonexistentwhitelistid
```
**Errorneous Response**
```http
404 Not Found
Content-type: application/json

{
    "error": {
        "code": 14014,
        "message": "whitelist id of otp app not found",
        "traceId": "283387d2a40757de"
    }
}
```