---
title: Using the API
permalink: /using-the-api/
nav_order: 50
---

# Using the API

> ⚠️ **Draft:** This page is a work in progress and may change.

Resource overview – a one-page map of major resources with short descriptions (Parties, Clients, Matters, Time Entries, Documents, Codes, Users, Search, Status). Tie each to a use case. 
GitHub

Client generation – how to generate SDKs from the OpenAPI (OpenAPI Generator / Swagger Codegen pointers).

## Pagination & filtering

Parameters (limit, offset) with defaults and max. 
GitHub

Any "updatedAfter/lastUpdated" semantics worth knowing for incremental syncs. 
GitHub

## Idempotency

Required header: Idempotency-Key (UUID v4 suggested), behavior on retry, guidance for retries/timeouts. 
GitHub

## Errors

Error envelope (code/message) and typical HTTP status codes. 
GitHub

Throttling/rate-limit errors (if applicable) and backoff guidance.

## Search

Global/scoped search endpoints, required q param, examples for common queries. 
GitHub

## Next Steps

[How-to](/how-to/)
