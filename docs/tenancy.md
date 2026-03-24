---
title: Tenancy Model
permalink: /tenancy-model/
nav_order: 40
---

# Tenancy Model

> ⚠️ **Draft:** This page is a work in progress and may change.

## Tenant Routing

The OneLaw Cloud platform is single-tenanted — each law firm runs its own isolated instance of the OneLaw Cloud Service, with its own data store and its own API endpoint.
As a result, there is no single global API base URL. Every request must be sent to the base URL belonging to the law firm whose data you are accessing.

To determine the correct base URL, your integration must first:

1. Authenticate a user through OAuth 2.0 (as described in the [Authentication](authentication.md) guide).
2. Use the resulting access token to query the OneLaw Configuration Service.
This service examines the token, reads the FirmCloudId claim inside it, and returns the API base URL for that user's firm.

## Requesting the firm's API base URL

You can discover the correct base URL by calling the configuration service endpoint:

```
GET https://config.onepractice.net/api/instance/production/firm
Authorization: Bearer {YOUR_ACCESS_TOKEN}
```

### Example response

```
200 OK
{
  "apiBaseUrl": "https://example.onepractice.net/api/v1"
}
```

The apiBaseUrl value identifies the root endpoint for that firm's API.
All subsequent API requests must be directed to this URL.

### Using the base URL

Once you have obtained the tenant's base URL, call API endpoints relative to it.
For example, to retrieve the list of parties:

```
GET https://example.onepractice.net/api/v1/parties
Authorization: Bearer {YOUR_ACCESS_TOKEN}
```

Every API request must include a valid access token in the Authorization header, and it must use the base URL returned from the configuration service.
The API will reject requests sent to the wrong tenant or without a valid token.

## Checking if the API Is Available

Not all law firms are running versions of OneLaw that include API access. Before making any API calls, you should confirm that the API is supported and reachable for the signed-in user's tenant.

Once you have a signed-in user and the base URL from the config service, send a request to the `GET /ping` endpoint:

```
GET https://firm123.api.onelaw.cloud/v1/ping
Authorization: Bearer {YOUR_ACCESS_TOKEN}
```

If the OneLaw Cloud instance supports the API, you'll receive a 200 OK response:

```
{
  "status": "ok",
  "timeStamp": "2025-11-07T07:34:18.376Z"
}
```

If the law firm is running a version of OneLaw Cloud Service that does not support the API, you'll receive a 404 Not Found response instead.

Status | Meaning
-------|--------
200 OK | The API is available and ready for use
404 Not Found | The tenant does not yet support the API
401 Unauthorized | The user's access token is missing or invalid

## Next Steps

[Data Model](/api/data-model/)
