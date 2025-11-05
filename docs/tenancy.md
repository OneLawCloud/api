---
title: Tenancy Model
permalink: /tenancy-model/
nav_order: 5
---

> ⚠️ **Draft:** This page is a work in progress and may change.

## How tenant routing works

The OneLaw Cloud platform is single-tenanted — each law firm runs its own isolated instance of the OneLaw Cloud service, with its own data store and its own API endpoint.
As a result, there is no single global API base URL. Every request must be sent to the base URL belonging to the firm whose data you are accessing.

To determine the correct base URL, your integration must first:

1. Authenticate a user through OAuth 2.0 (as described in the [Authentication](authentication.md) guide).
2. Use the resulting access token to query the OneLaw Configuration Service.
This service examines the token, reads the FirmCloudId claim inside it, and returns the API base URL for that user’s firm.

## Requesting the firm’s API base URL

You can discover the correct base URL by calling the configuration service endpoint:

```
GET https://config.onepractice.net/instance/production/firm
Authorization: Bearer {YOUR_ACCESS_TOKEN}
```

### Example response

```
200 OK
{
  "apiBaseUrl": "https://example.onepractice.net/api/v1"
}
```

The apiBaseUrl value identifies the root endpoint for that firm’s API.
All subsequent API requests must be directed to this URL.

### Using the base URL

Once you have obtained the tenant’s base URL, call API endpoints relative to it.
For example, to retrieve the list of parties:

```
GET https://example.onepractice.net/api/v1/parties
Authorization: Bearer {YOUR_ACCESS_TOKEN}
```

Every API request must include a valid access token in the Authorization header, and it must use the base URL returned from the configuration service.
The API will reject requests sent to the wrong tenant or without a valid token.

## Next Steps

[Using the API](usingtheapi.md)
