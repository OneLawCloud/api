## Authentication

Overview: OAuth 2.0 Authorization Code (Azure AD B2C)

Auth endpoints – authorization URL and token URL. 
GitHub

Scopes & meanings – access_as_user, openid, offline_access, with brief plain-English descriptions. 
GitHub

Tokens & claims – note the firm "cloudId" claim used for tenancy linkage if relevant in your access token docs. 
GitHub

Step-by-step example – full code path (PKCE recommended), token acquisition, calling a protected endpoint.

Token lifetimes & refresh – when to refresh (uses offline_access). 
GitHub

Common pitfalls – redirect URI mismatch, wrong scope, missing PKCE, etc.
