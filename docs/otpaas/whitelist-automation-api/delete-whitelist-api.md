# Delete Whitelists API
This API allows the deletion of a single of email domains / user email whitelist of a OTP app

## Request
```
DELETE /otp/namespace/{namespace}/apps/{appid}/whitelists/{whitelistid}
```
## Request Path Parameters
| Name        | Description                                           |
| ----------- | ----------------------------------------------------- |
| namespace   | The tenant namespace of which the OTP app belongs to. |
| appid       | The app id of your OTP app.                           |
| whitelistid | The id of the whitelist to delete                     |

## Request Query Parameters
No query parameters for this API

## Request Body Schema
No request body needed for this API

## Request Body Fields
No request body needed for this API

## Response
The response status would be `202 Accepted`.

## Response Body Schema
No response body is returned for this API.

## Response Body Properties
No response body is returned for this API.

## Usage Examples

?> Refer to the [Endpoints](/otpaas/whitelist-automation-api/intro?id=endpoints) section to view the staging and production Automation API endpoints.

Example for deleting a whitelist

**Request**
```http
DELETE https://{automation_api_endpoint}/otp/namespace/example_namespace/apps/example_app_id/whitelists/f64a12fb-8952-4b99-80ce-0052d42a3362

```
**Response**
```
202 Accepted

```
## Error Responses

Known error response include `404 Not Found` if the otp app id or the whitelist id supplied is non existent.
An example of this is shown below.

**Errorneous Request**
```http
DELETE https://{automation_api_endpoint}/otp/namespace/example_namespace/apps/example_app_id/whitelists/nonexistentwhitelistid
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