# Concepts

## Authorization & Access Control
To help us design a proper access control model, tenants need to answer two questions:

- **Who** has access to our application/service?
- **What** actions can they perform on our application/service?

### Coarse-Grain Access Control - The Who
In TechPass, Tenants can configure their applications to only allow authorised users to sign in. This is done by ensuring the [*User Assignment Required*](applications/#user-assignment-required) setting is checked. With this setting enabled, any user who wishes to sign in to your application will need to be assigned access to the app. This allows the tenant to perform pre-approval checks on the user before access is granted. This is useful for application that are/should be accessible only to a specific subset of users. 

?> Assigning access to users can be programmatically via TechPass's [*Automation APIs*](apis). The APIs provides tenants with the versatility to implement automated pre-approval checks in their backend and then call the relevant API to assign access once the checks are completed.

For example, a finance & billing service should only be accessed by project manager and finance executives. The tenant owner can carry out their own verification before granting access.

Alternatively, you can uncheck the setting to allow ANY user with a TechPass account to sign in to your app. For example, a community forum for SGTS. Anybody is welcome to enter and participate as long as he is has a verified identity.

### Fine-Grain Access Control - The What
Once access to sign in is granted, the next question to consider would be what actions can the user perform in the application? TechPass can help tenants to address this in several ways, depending on the use case and capability of the application.

Off-the-shelf proprietary applications should already come with some sort of access control mechanism built-in. Depending on the capability of the application, it may not be possible for tenants to manage permissions via TechPass. Tenants will need manage access locally on these applications using the built-in feature.

Certain proprietary applications do support features that allow TechPass to manage permission through the use of groups. These applications can read the group property value from the *access token* and *id token* that is issued by TechPass and assign permission according to the group that the user is in. Tenants will need to consult the technical documentations to figure how to configure their applications to read this information.

?> To allow an application access to the group information of a user, tenants will need to create a group, assign the group to the application and then add the user into the group. This is to ensure relevant application privilege information for the user is returned via the tokens.

For applications that are developed from the ground up, careful consideration should be done to determine if the access control capabilities should be fully built into the application or make use of the token information that is returned by TechPass AAD. There's no correct way of doing it, but only the preferred way. 

!> There's a limit of 50 user groups for each namespace. Tenants will need to take this into consideration when deciding which method to adopt. 



## External References
- [Azure AD Documentation](https://docs.microsoft.com/en-us/azure/active-directory/)
- [IETF OAuth 2.0 Spec](https://tools.ietf.org/html/rfc6749)
- [OpenID Connect 1.0](https://openid.net/connect/)
- [SAML 2.0](http://docs.oasis-open.org/security/saml/Post2.0/sstc-saml-tech-overview-2.0.html)
