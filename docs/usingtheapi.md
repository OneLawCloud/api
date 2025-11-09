---
title: Using the API
permalink: /using-the-api/
nav_order: 50
---

# Using the API

> ⚠️ **Draft:** This page is a work in progress and may change.

The API follows REST principles and uses JSON encoded in UTF-8 for all request and response bodies.
You’ll need an access token to authenticate (see [Authentication](/api/authentication/)
).
This page covers common patterns such as pagination, filtering, and error responses.

## Parties, Clients & Users

The OneLaw Cloud Service models all entities — people, companies, trusts, partnerships, couples, and so on — as Parties.

In addition to standard details such as names and addresses, each Party has a type and may have one or more roles.

### Party Types

Every Party has exactly one Party Type, made up of two components: a System Party Type and a Party Type Name.

#### System Party Type

This is a system-defined enumeration that classifies every Party into exactly one of the following categories:

1. Natural Parties – individuals or people
2. Legal Parties – companies or organizations
3. Multi Parties – entities such as trusts, partnerships, or couples

#### Party Type Name

This is a user-editable text field that law firms can customize to reflect the specific types of parties they interact with — for example, Company, Individual – NZ Resident, or Couple.

### Party Roles

Parties can participate in one or more Party Roles, depending on their relationship with the law firm.

Role | Description
-----|------------
None | Many parties have no defined roles — for example, counterparties on matters or billing contacts.
Client | Parties who are clients of the law firm hold this role, which grants them access to legal matters, time recording, billing, and related functions.
User | Law firm staff members are also represented as Parties, each with a User role. Some Users may also be Clients.

## Dates & Times

Most date and time values in the API represent **timestamps** — specific points in time — and are provided in **UTC** using the [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) format (e.g. `2025-11-07T03:45:00Z`).

However, some fields represent **calendar dates** rather than precise timestamps (for example, a due date or a document date).  
These values are stored and returned **exactly as entered**, in **local date/time format**, and **should not be interpreted as UTC timestamps**.

## Pagination & Filtering

Large collection endpoints support pagination via limit and offset query parameters.

Parameter | Type   | Description
----------|------  |------------
limit     | integer| Number of results per page
offset    | integer| Skip this many results

**Example:**
```
GET /parties?limit=100&offset=200
```

Pagination results are always provided in the following structure:

``` json
{
    "items": [ /* items */ ],
    "total": 99,
    "lastUpdated": "2025-01-01T00:00:00Z"
}
```

**total** is the total number of items that are available given the current filter parmeters. 

**lastUpdated** is the most recent updated date of all the items in the filtered results. If the lastUpdated date changes between paging requests, then the collection has changed and you may want to start paging over again from the beginning.

## Filtering

Many endpoints support filtering on specific fields (see individual endpoint docs). In some cases boolean
operations are available.

**Example:** get parties that have a phone and an email address
```
GET /parties?contactableBy=phone&contactableBy=address
```

**Example:** get parties that have a phone **or** an email address

```
GET /parties?contactableBy=phone,email
```

## Party Sync

The parties endpoint supports bulk data syncronization via an initial full, paged data load followed by repeated incremental loads.

**Example:** full data load

```
GET /parties?offset=0&limit=500
GET /parties?offset=500&limit=500
GET /parties?offset=1000&limit=500
...etc...
```

**Example:** incremental data load using the updatedAfter parameter (ISO 8601 timestamp) to fetch only records changed since your last sync:

```
GET /parties?updatedAfter=2025-01-01T00:00:00Z
```

## Idempotency

Certain write operations (POST, PATCH) should include an Idempotency-Key header.
This prevents accidental duplication when retrying requests due to network errors or timeouts.

**Example:**

```
POST /matter/{matterId}/time-entries
Idempotency-Key: 7e9a8d02-beb2-4e7e-a822-3aee9b8b16f9
```

If the same key is reused within 24 hours, the API returns the original response.

## Errors

All errors responses include a relevant HTTP status code and a standard JSON body.

**Example**: 400 - Bad Request
```
{
  "code": "VALIDATION",
  "message": "Document name required"
}
```

**Example**: 401 - Unauthorized
```
{
  "code": "AUTH_FAILED",
  "message": "Auth token is missing or invalid"
}
```

**Example**: 403 - Forbidden
```
{
  "code": "FORBIDDEN",
  "message": "Access denied"
}
```

**Example**: 404 - Not Found
```
{
  "code": "NOT_FOUND",
  "message": "Party not found"
}
```

**Example**: 422 - Unprocessable Entity
```
{
  "code": "VALIDATION",
  "message": "Cannot convert upload to PDF"
}
```

**Example**: 500 - Internal Server Error
```
{
  "code": "SYSTEM",
  "message": ""
}
```

## Request Limits and Usage Guidelines

At present, we do not enforce a strict rate limit or throttle for API requests. However, in order to ensure the reliability and stability of the service, we reserve the right to impose limits or throttling in the future — either globally or on a per-client, per-endpoint basis. 

We therefore recommend that integrators adopt a practically reasonable request cadence (for example, avoiding very high-frequency polling loops) and implement robust retry/back-off logic. Doing so will help avoid unintended service degradation and position your implementation well in the event of any future changes in usage policy.

## Next Steps

[How-to](/api/how-to/)
