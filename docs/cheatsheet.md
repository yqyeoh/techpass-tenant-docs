# TechPass Cheatsheet
This page documents the dos, don'ts, wants and needs when using TechPass for your applications/services

## App to App (OAuth 2.0 client credentials grant)
[OAuth 2.0 client credentials grant](/concepts/clientcred) depicts an authentication and authorization grant flow that establish the communication between system to system.
It uses certificates and secrets for the authentication part of the flow.

Having said that, this flow is to allow you to establish trust between your applications/systems and TechPass.
It is **NOT** used for established trust between your user's applications/systems with your applications/systems.
Meaning, you should **NOT** be using TechPass to authenticate your user's systems. It is not App to App. It is App to TechPass.


## Automation APIs (the don'ts)
[Automation API](/apis/integration) is provided to allow you to manage the SSO and IAM setups for your namespace via REST apis.
In order to make the API calls, you will need to establish the communication using clients credential grant.

You should **NOT** allow your users to make these API calls at all.
This will pose a security risk. TechPass will **not** be liable if there are any malicious activities conducted via REST apis using your credentials.
Needless to say, always keep your credentials (certificates and secrets) secure. 
Always in a secret manager and **never** hard coded!

## Cross-Site Request Forgery
Another great writeup on CSRF attack prevention by OWASP:
https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html

## IM8, CSG requirements??

Password mgmt
account lifecyle
encouragement of auth code grant and avoid implicit
Account lockout - after 10 attempts?

link to im8 and csg requiremnt docs


## Session Management
TechPass will not control what happens after authentication and authorization. 
It is up to individual product teams to manage the session for your applications and services.
There are, however, some useful reminders:
1. Never expose any tokens to the frontend/client browser. ID, Access and Refresh tokens should always be kept secure in the backend.
2. The session id generated and passed to the frontend should always be a nondescript unique value.
3. Always keep the sessions short. This helps to reduce the exposure to session-based attacks. You can consider tagging the sessions to TechPass sessions for your logged-in users.  
   The Access tokens are always short-lived (5-10 mins). You can tag your session time to token expiration. You can use the refresh token to renew the access token should you need to extend the session.
   
There many considerations when managing the user session. OWASP had a detailed article on how to best implement session, do refer to it:
https://cheatsheetseries.owasp.org/cheatsheets/Session_Management_Cheat_Sheet.html

