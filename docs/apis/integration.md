## Automation API OpenAPI Spec
The full specification of the Autiomation API is published in here:  
[Staging](https://stg.docs.developer.gov.sg/docs/techpass-automation-api/)  
[Production](https://docs.developer.gov.sg/docs/techpass-automation-api/)

Please refer to [cheatsheet](/cheatsheet?id=automation-apis-the-dos-and-don39ts) for the dos and don'ts of using Automation APIs

## Required Keys and Tokens
For the Tenant's Automation Application to make the Automation API requests, two essential data are needed:

### API Key
1. This is the AWS API Gateway authentication mechanism used for the application to make HTTPS requests to the Automation API endpoint.
2. Note that this allows TechPass to throttle the number of API requests made by the tenants. The exact threshold has yet to be decided.

### Access Token
1. The access token encapsulates the requester's security identity and should be used as the bearer token for all HTTPS requests made to the Automation API endpoint. This will authorise the request to allow operations based on the tenant's namespace.
2. There are 2 ways to request for an access token:
    * Use Client Assertion (certificates) – this is the recommended approach
    * Use Client Secret

## Example cURL Usage
### Request for Access Token Using Client Assertion
1. The OAuth 2.0 token endpoint (v2) is **POST** https://login.microsoftonline.com/{DirectoryID}/oauth2/v2.0/token.
    * For **Directory ID**, please refer to the [How To Get The Directory/Tenant ID](/apis/tenant-id.md).
2. The request payload for the token endpoint requires the **client_id**, **client_assertion**, **client_assertion_type** and **scope**.
    * For **client_id**, please refer to the [How To Get The Application/Client ID](/apis/client-id.md).
    * For **client_assertion**, please refer to the following documentations:
        * [Microsoft identity platform application authentication certificate credentials](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials)
        * [Microsoft identity platform and the OAuth 2.0 client credentials flow (Second case: Access token request with a certificate)](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow#second-case-access-token-request-with-a-certificate)
        * For **client_assertion_type**, use ```urn:ietf:params:oauth-client-assertion-type:jwt-bearer```
        * For **scope**, it should be `{automation_api_endpoint}/.default`. Refer to [Endpoints](#Endpoints) for the Automation API Endpoints.
3. Once you've retrieved a **valid access token**, use it as the **bearer token** for succeeding Automation API requests.

### Command

```
curl -X POST 'https://login.microsoftonline.com/edf6f660-7319-45db-8531-fdbd46047921/oauth2/v2.0/token' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'client_id=51d7fe82-f122-4651-a4fd-262c2b2df967&grant_type=client_credentials&client_assertion=eyJhbGciOiJFUzI1NiJ9.ewogICJqdGkiOiJteUpXVElkMDAxIiwKICAic3ViIjoiMzgxNzQ2MjM3NjIiLAogICJpc3MiOiIzODE3NDYyMzc2MiIsCiAgImF1ZCI6Imh0dHA6Ly9sb2NhbG
 hvc3Q6NDAwMC9hcGkvYXV0aC90b2tlbi9kaXJlY3QvMjQ1MjMxMzgyMDUiLAogICJleHAiOjE1MzYxNjU1NDAsCiAgImlhdCI6MTUzNjEzMjcwOAp9Cg.YB4gdhWUGRjWEsEbKDs7-G2WFH2oYz7bAEP5AtegHXInkY9ncA2V3IoA6O_HVQuFxyCRIklrxsMk32MfNF_ABA&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer&scope=https://api.stg.techpass.suite.gov.sg/.default'
```

### Output
```
{
  "token_type": "Bearer",
  "expires_in": 1199,
  "ext_expires_in": 1199,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkU1WWI4TFlCZFpHN3E4aExZajhCT2VpTGZuOUt6d2M4WjRnZXpNdklZT0kiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFqS1doWHNsSFJfS1hFZyIsImtpZCI6Im5PbzNaRHJPRFhFSzFqS1doWHNsSFJfS1hFZyJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9lZGY2ZjY2MC03MzE5LTQ1ZGItODUzMS1mZGJkNDYwNDc5MjEvIiwiaWF0IjoxNjEyMzIyMDY0LCJuYmYiOjE2MTIzMjIwNjQsImV4cCI6MTYxMjMyMzU2NCwiYWlvIjoiRTJaZ1lFZzNWZG1mNmhzcHYvdVBNYmZmOC9NQ0FBPT0iLCJhcHBfZGlzcGxheW5hbWUiOiJzaGlwOkF1dG9tYXRpb24iLCJhcHBpZCI6ImUyOTBhOTFjLTg0NjktNDgwOC1iOWZjLTc2OWI4MjgwN2YwNSIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0L2VkZjZmNjYwLTczMTktNDVkYi04NTMxLWZkYmQ0NjA0NzkyMS8iLCJpZHR5cCI6ImFwcCIsIm9pZCI6IjNlNmIyZTg3LTBjNjktNGMxMS1hMGYzLWJmMGY0MTU4ZjlhYiIsInJoIjoiMC5BQUFBWVBiMjdSbHoyMFdGTWYyOVJnUjVJUnlwa09KcGhBaEl1ZngybTRLQWZ3VS1BQUEuIiwic3ViIjoiM2U2YjJlODctMGM2OS00YzExLWEwZjMtYmYwZjQxNThmOWFiIiwidGVuYW50X3JlZ2lvbl9zY29wZSI6IkFTIiwidGlkIjoiZWRmNmY2NjAtNzMxOS00NWRiLTg1MzEtZmRiZDQ2MDQ3OTIxIiwidXRpIjoibGJxWEc5cGxFRWltdmxYNFh6QnBBQSIsInZlciI6IjEuMCIsInhtc190Y2R0IjoxNTgxNjYxOTMxfQ.mSgI4yi_AH-zEZEexx6LDx6cGPIvVkY7B4EnjKcf0vDvuU9PxB0ugzVEpt9QVAG8vIKoLY_Bb5Ic8pu5um7dPE0pLY0O-zNSI23BijOMc91wP8QD8UyhEmWPZLRAOLv8CTzHCiL9O5hlpJPVIwq3FAZgKhCrpAbdRarithxeBnb6fg7ady1Z4mfHh3WmNpK86z9DEQ1nL9QL5pmTYS27DBk9nnYjH1a4O95VUNoJU1H50rPyo95vDu2yuOYgeutJxT6Hmp9u8IP8P0MFrHsAcuv6TvojTQ8yR506nRcH797z40L2VDrjwM2MHrm8IKx0MAhyGBrQarnwAaQiUV7wPw"
}
```

### Request for Access Token Using Client Secret
1. The OAuth 2.0 token endpoint (v2) is **POST** https://login.microsoftonline.com/{DirectoryID}/oauth2/v2.0/token.
    * For **Directory ID**, please refer to the [How To Get The Directory/Tenant ID](/apis/tenant-id.md).
2. The request payload for the token endpoint requires the **client_id**, **client_secret** and **scope**.
    * For **client_id**, please refer to the [How To Get The Application/Client ID](/apis/client-id.md).
    * For **client_secret**, please refer to the [How To Get The Client Secret](/apis/client-secret.md).
    * For **scope**, it should be `{automation_api_endpoint}/.default`. Refer to [Endpoints](#Endpoints) for the Automation API Endpoints.
3. Once you've retrieved a **valid access token**, use it as the **bearer token** for succeeding Automation API requests.

### Command
```
curl -X POST 'https://login.microsoftonline.com/edf6f660-7319-45db-8531-fdbd46047921/oauth2/v2.0/token' \
  -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'client_id=51d7fe82-f122-4651-a4fd-262c2b2df967&grant_type=client_credentials&client_secret=R57BtGiDY71hIa~J~-5foTtwx-~1a5et~W&scope=https://api.stg.techpass.suite.gov.sg/.default'
```

### Output
```
{
  "token_type": "Bearer",
  "expires_in": 1199,
  "ext_expires_in": 1199,
  "access_token": "eyJ0eXAiOiJKV1QiLCJub25jZSI6IkU1WWI4TFlCZFpHN3E4aExZajhCT2VpTGZuOUt6d2M4WjRnZXpNdklZT0kiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFqS1doWHNsSFJfS1hFZyIsImtpZCI6Im5PbzNaRHJPRFhFSzFqS1doWHNsSFJfS1hFZyJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9lZGY2ZjY2MC03MzE5LTQ1ZGItODUzMS1mZGJkNDYwNDc5MjEvIiwiaWF0IjoxNjEyMzIyMDY0LCJuYmYiOjE2MTIzMjIwNjQsImV4cCI6MTYxMjMyMzU2NCwiYWlvIjoiRTJaZ1lFZzNWZG1mNmhzcHYvdVBNYmZmOC9NQ0FBPT0iLCJhcHBfZGlzcGxheW5hbWUiOiJzaGlwOkF1dG9tYXRpb24iLCJhcHBpZCI6ImUyOTBhOTFjLTg0NjktNDgwOC1iOWZjLTc2OWI4MjgwN2YwNSIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0L2VkZjZmNjYwLTczMTktNDVkYi04NTMxLWZkYmQ0NjA0NzkyMS8iLCJpZHR5cCI6ImFwcCIsIm9pZCI6IjNlNmIyZTg3LTBjNjktNGMxMS1hMGYzLWJmMGY0MTU4ZjlhYiIsInJoIjoiMC5BQUFBWVBiMjdSbHoyMFdGTWYyOVJnUjVJUnlwa09KcGhBaEl1ZngybTRLQWZ3VS1BQUEuIiwic3ViIjoiM2U2YjJlODctMGM2OS00YzExLWEwZjMtYmYwZjQxNThmOWFiIiwidGVuYW50X3JlZ2lvbl9zY29wZSI6IkFTIiwidGlkIjoiZWRmNmY2NjAtNzMxOS00NWRiLTg1MzEtZmRiZDQ2MDQ3OTIxIiwidXRpIjoibGJxWEc5cGxFRWltdmxYNFh6QnBBQSIsInZlciI6IjEuMCIsInhtc190Y2R0IjoxNTgxNjYxOTMxfQ.mSgI4yi_AH-zEZEexx6LDx6cGPIvVkY7B4EnjKcf0vDvuU9PxB0ugzVEpt9QVAG8vIKoLY_Bb5Ic8pu5um7dPE0pLY0O-zNSI23BijOMc91wP8QD8UyhEmWPZLRAOLv8CTzHCiL9O5hlpJPVIwq3FAZgKhCrpAbdRarithxeBnb6fg7ady1Z4mfHh3WmNpK86z9DEQ1nL9QL5pmTYS27DBk9nnYjH1a4O95VUNoJU1H50rPyo95vDu2yuOYgeutJxT6Hmp9u8IP8P0MFrHsAcuv6TvojTQ8yR506nRcH797z40L2VDrjwM2MHrm8IKx0MAhyGBrQarnwAaQiUV7wPw"
}
```

### Example
As an example, we can call the **GET /iam/namespace/:namespace/groups** API by providing the **api key** and the **bearer token** to the request as shown below:
```
curl 'https://api.dev.techpass.gov.sg/iam/namespace/MYNAMESPACE/groups' \
  -H 'x-api-key: dOwPeMjDcL9sxf6eChJAY1uidoDWnarjagKfpuwS' \
  -H 'Accept: application/json' \
  -H 'Authorization: Bearer eyJ0eXAiOiJKV1QiLCJub25jZSI6IkU1WWI4TFlCZFpHN3E4aExZajhCT2VpTGZuOUt6d2M4WjRnZXpNdklZT0kiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5PbzNaRHJPRFhFSzFqS1doWHNsSFJfS1hFZyIsImtpZCI6Im5PbzNaRHJPRFhFSzFqS1doWHNsSFJfS1hFZyJ9.eyJhdWQiOiJodHRwczovL2dyYXBoLm1pY3Jvc29mdC5jb20iLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC9lZGY2ZjY2MC03MzE5LTQ1ZGItODUzMS1mZGJkNDYwNDc5MjEvIiwiaWF0IjoxNjEyMzIyMDY0LCJuYmYiOjE2MTIzMjIwNjQsImV4cCI6MTYxMjMyMzU2NCwiYWlvIjoiRTJaZ1lFZzNWZG1mNmhzcHYvdVBNYmZmOC9NQ0FBPT0iLCJhcHBfZGlzcGxheW5hbWUiOiJzaGlwOkF1dG9tYXRpb24iLCJhcHBpZCI6ImUyOTBhOTFjLTg0NjktNDgwOC1iOWZjLTc2OWI4MjgwN2YwNSIsImFwcGlkYWNyIjoiMSIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0L2VkZjZmNjYwLTczMTktNDVkYi04NTMxLWZkYmQ0NjA0NzkyMS8iLCJpZHR5cCI6ImFwcCIsIm9pZCI6IjNlNmIyZTg3LTBjNjktNGMxMS1hMGYzLWJmMGY0MTU4ZjlhYiIsInJoIjoiMC5BQUFBWVBiMjdSbHoyMFdGTWYyOVJnUjVJUnlwa09KcGhBaEl1ZngybTRLQWZ3VS1BQUEuIiwic3ViIjoiM2U2YjJlODctMGM2OS00YzExLWEwZjMtYmYwZjQxNThmOWFiIiwidGVuYW50X3JlZ2lvbl9zY29wZSI6IkFTIiwidGlkIjoiZWRmNmY2NjAtNzMxOS00NWRiLTg1MzEtZmRiZDQ2MDQ3OTIxIiwidXRpIjoibGJxWEc5cGxFRWltdmxYNFh6QnBBQSIsInZlciI6IjEuMCIsInhtc190Y2R0IjoxNTgxNjYxOTMxfQ.mSgI4yi_AH-zEZEexx6LDx6cGPIvVkY7B4EnjKcf0vDvuU9PxB0ugzVEpt9QVAG8vIKoLY_Bb5Ic8pu5um7dPE0pLY0O-zNSI23BijOMc91wP8QD8UyhEmWPZLRAOLv8CTzHCiL9O5hlpJPVIwq3FAZgKhCrpAbdRarithxeBnb6fg7ady1Z4mfHh3WmNpK86z9DEQ1nL9QL5pmTYS27DBk9nnYjH1a4O95VUNoJU1H50rPyo95vDu2yuOYgeutJxT6Hmp9u8IP8P0MFrHsAcuv6TvojTQ8yR506nRcH797z40L2VDrjwM2MHrm8IKx0MAhyGBrQarnwAaQiUV7wPw'
```

Output
```
{
  "groups": [
    {
      "created": "2020-09-16T02:59:41Z",
      "description": "-",
      "grn": "ship:group 1",
      "id": "35176114-74d0-4d7e-af7c-13e5775d4016",
      "name": "group 1"
    },
    {
      "created": "2021-01-19T09:47:52Z",
      "description": "ship:test",
      "grn": "ship:group 2",
      "id": "7682dee2-1b75-4349-bdfc-f4e60d4d5b36",
      "name": "group 2"
    },
    {
      "created": "2020-10-15T03:29:59Z",
      "description": "",
      "grn": "ship:group 3",
      "id": "505d42b4-ceaf-40cc-8df8-819e04405bc2",
      "name": "group 3"
    }
  ]
}
```
## Change In Access Token Scope
We will be implementing a change to the **scope** parameter in the request for access token. You are required to change the **scope** parameter value from `https://graph.microsoft.com/.default` to `{automation_api_endpoint}/.default` (refer to [Endpoints](#Endpoints) for the Automation API Endpoints). 

To ease the transition, this change will be backward compatible (i.e. we will continue to accept access token with `scope` value of `https://graph.microsoft.com/.default`) for **2 months** from the Change Effective Date. This will become a breaking change after that period, where only `{automation_api_endpoint}/.default` will be accepted.

| Environment | Change Effective Date | Backward Compatible Until |
| ----------- | ----------------------|---------------------------|
| STG         | 20 Jul 2022           | 20 Sep 2022               |
| PROD        | TBA                   | TBA                       |

Refer to [Example cURL Usage](#example-curl-usage) on how to specify the **scope** in your request for access token.

## Validating Access Token
This is only applicable for the new [change](#Change-In-Access-Token-Scope) in **scope** parameter.

Upon receiving the access token, you should check for 2 things:
* **token signature**

  1. parse the JWT access token and make note of the **kid** value. The algorithm in used is **RS256**
      ```
      {
        "typ": "JWT",
        "alg": "RS256",
        "kid": "GvnPApfWMdLRi8PDmisFn7bprKg"
      }
      ```

  2. Make a GET call to https://login.microsoftonline.com/{DirectoryID}/discovery/v2.0/keys. 
      * For **Directory ID**, please refer to the [How To Get The Directory/Tenant ID](/apis/tenant-id.md).  
  
  3. Parse the results returned from **/keys** endpoint and find the record matching **kid** value. Extract the **x5c** value then enclose the string in the BEGIN CERTIFICATE / END CERTIFICATE block. This will form the public key used to verify the signature. For instance, using the NPM package [jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken), you can do it like this:
  ```
    import jwt = require('jsonwebtoken');

    const x5c= getX5CValue() // refer step 1-3 above
    
    const decodedValidToken = (accessToken: string, x5c: string) => {
      const key: string = `-----BEGIN CERTIFICATE-----\n${x5c}\n-----END CERTIFICATE-----`;

      // decode & verify token
      return jwt.verify(accessToken, key);
    }

    const authorizationHeader: string = req.headers.authorization;
    const decodedToken = (decodedValidToken(authorizationHeader.replace('Bearer ','')) as any);
  ```

  The token signature is considered valid if no errors were thrown and you got a token back.

  If errors were thrown, you can check for specific issues with the validation process, as documented in the jsonwebtoken NPM package: [Errors & Codes](https://github.com/auth0/node-jsonwebtoken#errors--codes)

* **'aud' claim**  
  Check that the **aud** claim of the decoded token points to [Automation API Endpoint](#Endpoints)

## Endpoints
| Environment | Automation API Endpoint         |
| ----------- | ------------------------------- |
| STG         | https://api.stg.techpass.suite.gov.sg |
| PROD        | https://api.techpass.gov.sg     |