# Authentication (DRAFT)

## Overview

The OneLaw Cloud API uses the standard OAuth 2.0 Authorization Code flow to authenticate users and authorize access to their firm’s data.
This is the same protocol used by many modern APIs and identity systems. Your integration application (“client”) redirects a user to sign in, the user grants consent, and your app receives an authorization code that can be exchanged for an access token.

Each integration must be registered with OneLaw before use.
During registration, you must supply one or more redirect URIs (where the user will be returned after sign-in).
After registration, OneLaw will issue you a Client ID and Client Secret, which your application uses in the authorization and token exchange process.

## Authorization and Token Endpoints

Endpoint Type | URL
--------------|----
Authorization URL | https://onelawcloud.b2clogin.com/onelawcloud.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SignIn
Token URL | https://onelawcloud.b2clogin.com/onelawcloud.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1A_SignIn

(These URLs are also published in the OpenAPI specification.)

## Scopes

Scope | Description
------|------------
access_as_user | Grants access to the OneLaw Cloud API on behalf of the signed-in user.
openid | Requests an OpenID Connect ID token so the user’s identity can be confirmed.
offline_access | Allows your app to receive a refresh token, enabling silent sign-in and token renewal without user interaction.

Your app should request all three scopes unless you have a specific reason to omit one.

Example:

```
scope=access_as_user openid offline_access
```

## Tokens

When your app exchanges an authorization code, the token endpoint returns:

- access_token – used in the Authorization: Bearer <token> header for all API calls.
- refresh_token – used to obtain a new access token without user interaction.
- id_token – (optional) an identity token containing standard OpenID Connect claims.

Access tokens include a OneLaw-specific claim called FirmCloudId, which identifies the tenant (law firm) that the signed-in user belongs to.
You don’t need to handle this claim directly — when you pass the access token to the OneLaw configuration service, that service reads the claim and returns the correct API base URL for the firm.

## Step-by-Step Example

There are many ways to perform the OAuth 2.0 Authorization Code flow, depending on the language or framework you use.
Most major platforms provide robust client libraries and SDKs that handle the details of URL construction, redirects, and token exchange for you.

Here are some common options used by OneLaw integrators:

Language / Platform | Recommended Library or Tool
--------------------|----------------------------
.NET / C# | Microsoft.Identity.Client (MSAL.NET)
JavaScript / Node.js | openid-client or passport-azure-ad
Python | Authlib or requests-oauthlib
Java | Spring Security OAuth2 Client
Postman | Built-in Authorization → OAuth 2.0 support
Browser-only apps | Use a front-end library such as MSAL.js

Each of these libraries follows the same underlying protocol — the standard OAuth 2.0 Authorization Code flow — but differ in syntax and framework integration.

The example below demonstrates the process using only a browser and curl, so you can clearly see what happens at each step, regardless of which library or SDK you later choose to implement.

### 1. Direct the user to sign in

Construct the authorization URL:

```
https://onelawcloud.b2clogin.com/onelawcloud.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SignIn
  ?client_id={YOUR_CLIENT_ID}
  &response_type=code
  &redirect_uri={YOUR_REDIRECT_URI}
  &scope=access_as_user openid offline_access
  &state=12345
```

Open this URL in a browser.
The user signs in with their OneLaw credentials and, if successful, the browser is redirected to your redirect URI with a code parameter in the query string:

https://yourapp.example.com/oauth/callback?code=ABC123

### 2. Exchange the code for tokens

Use curl (or your HTTP library) to POST the code to the token endpoint:

```
curl -X POST https://onelawcloud.b2clogin.com/onelawcloud.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1A_SignIn \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=authorization_code" \
  -d "client_id={YOUR_CLIENT_ID}" \
  -d "client_secret={YOUR_CLIENT_SECRET}" \
  -d "code=ABC123" \
  -d "redirect_uri={YOUR_REDIRECT_URI}" \
  -d "scope=access_as_user openid offline_access"
```

The response will include access_token, refresh_token, and id_token fields.

### 3. Call a protected endpoint

Use the access token in the Authorization header:

```
curl https://api.onelawcloud.co.nz/some-endpoint \
  -H "Authorization: Bearer eyJ0eXAiOiJKV..."
```

If the token is valid, the API will return data for the signed-in user’s firm.

### Token Lifetime and Refresh

Access tokens are short-lived (typically 1 hour).

Refresh tokens remain valid for a longer period (typically 14 days, subject to user activity).

When your app receives an invalid_token or HTTP 401 response, or when the access token nears expiry, request a new one using the refresh token:

```
curl -X POST https://onelawcloud.b2clogin.com/onelawcloud.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1A_SignIn \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "grant_type=refresh_token" \
  -d "client_id={YOUR_CLIENT_ID}" \
  -d "client_secret={YOUR_CLIENT_SECRET}" \
  -d "refresh_token={YOUR_REFRESH_TOKEN}"
```

A new access_token and refresh_token will be returned. Replace stored tokens accordingly.

## Common Pitfalls

Issue | Explanation / Fix
------|------------------
Redirect URI mismatch | The URI in your authorization request must exactly match one registered with OneLaw (including scheme, domain, and trailing slash).
Missing or incorrect scopes | If you omit access_as_user, the API will reject requests. Always include openid and offline_access for full functionality.
Invalid client credentials | Ensure your client secret hasn’t expired and is sent in the token exchange step, not the initial authorize request.
Using an expired access token | Refresh tokens before expiry; don’t reuse expired access tokens.
Calling the API before discovering the base URL | Always use the configuration service to obtain the tenant’s API base URL before making API calls.
Incorrect Content-Type | The token request must use application/x-www-form-urlencoded, not JSON.

## Next Steps

[Tenancy Model](tenancy.md)
