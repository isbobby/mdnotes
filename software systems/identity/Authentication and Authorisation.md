Authentication - verify user is who they say they are.
Authorisation - after user is authenticated, determine what they are allowed to do.

## Mechanisms
### Username/Password Based
Requires credential storage.
Rate limit and password complexity constraints to protect against brute force.
Also read: `bcrypt` - strong hashing algorithm
### MFA
Time based OTP
SMS/Email-Based Codes, less secure than time based OTP
Hardware tokens
### OAuth 2.0
Third party authentication.
### Single Sign-On (SSO)\
SAML, OAuth 2.0, OpenID connect.

## Session Management
After a user is authenticated, the server needs to retain this information. This authenticated information is referred to as session.
### Session Based (Cookies)
Server can return a session ID to client, and the client needs to attach this ID in every request.
The server also needs to store this session ID to check against in every request.
Same site / cross site cookies.
### Token based (JWT)
JWT are tokens containing user claims (user ID, roles, expiration).
The client sends JWT in each request.
### Refresh Tokens
Provide a refresh token for user, allowing them to be authenticated when their session expires.
## Authorisation Systems
### Role Based
### Attribute Based
### Access Control List (ACL)
### Policy Based
## Database Design
## Security Considerations
### Token Security
### XSRF & XSS 