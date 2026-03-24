---
title: Webhooks
permalink: /webhooks/
nav_order: 55
---

# Webhooks

> ⚠️ **Draft:** This page is a work in progress and may change.

## Webhooks (BETA)

You can subscribe to receive webhook notifications for party, matter and document changes in OneLaw Cloud. In the OneLaw App go to Administration > Integrations > Webhook Subscriptions. Add a new subscription record with the URL that you want to receive the webhook notification.

Notes:

1. The URL must use the HTTPS scheme
1. All webhook requests will be sent with the `POST` method
1. A signing key is required and is used to add an HMAC signature to the webhook request. You should validate each webhook request using the method described here (code samples also provided):
   - <https://github.com/standard-webhooks/standard-webhooks/blob/main/spec/standard-webhooks.md#verifying-webhook-authenticity>
   - <https://github.com/standard-webhooks/standard-webhooks/tree/main/libraries>
1. All webhook requests will include the following headers:
   - `Webhook-id` - an idempotent Webhook-id header that will not change on subsequent retries
   - `Webhook-timestamp` - timestamp for when the HTTP request was sent in unix epoch format
   - `Webhook-signature` - HMAC-256 signature
1. All webhook requests will have body content with a JSON-encoded object with the following top-level fields
   - `firmId` - ID of the law firm tenant that sent the webhook request
   - `id` - copy of the `webhook-id` header
   - `type` - the type of the event (e.g. "party.created"), determines the schema of the `data` object
   - `timestamp` - timestamp for when the HTTP request was sent in ISO 8601 UTC
   - `data` - the data associated with the event, schema determined by the `type` field
1. The schema for the JSON body content is defined in the OAS / Swagger spec, in the following schema:
   - `PartyWebhookRequest`
   - `MatterWebhookRequest`
   - `DocumentWebhookRequest`

## Next Steps

[How-To](/api/how-to/)
