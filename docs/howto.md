---
title: How-To
permalink: /how-to/
nav_order: 60
---

# How-To

> ⚠️ **Draft:** This page is a work in progress and may change.

## Checking if the API Is Available

Before making any API calls, you should confirm that the API is supported and reachable for the signed-in user's tenant.

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

## Document Uploads

The OneLaw Cloud Service supports a multi-stage process for uploading and managing documents. This design ensures large files are handled efficiently and that uploads can be flexibly associated with different entities (such as Matters or Parties).

### Overview of the Upload Process

Uploading a document involves four distinct steps:

1. Request an Upload Ticket

    Begin by calling `GET /uploads/ticket`.
    The response includes a pre-signed upload URL and an upload ID. These allow your client application to securely upload the binary file directly to cloud storage without routing the data through the OneLaw API servers.

2. Upload the File
    
    Perform a PUT request to the upload URL provided in the ticket.
    The request body should contain the binary content of the file you want to upload.
    This step completes the transfer of the raw document into temporary storage.

3. (Optional) Request a File Conversion

    If you need to convert the uploaded file into another format, you can call:
    `POST /uploads/{uploadId}/conversions`
    Currently, the service supports converting HTML documents to PDF.
    This step is optional and may be skipped if no conversion is required.

4. Store the Document in OneLaw
    
    Once the file (and any conversion) is complete, associate it with a specific record by posting to one of the following endpoints:

    `POST /matters/{matterId}/documents`

    `POST /parties/{partyId}/documents`

    In the request body, include:

    - The uploadId from step 1
    - Metadata such as document name, type, and description

    This final step registers the document within the OneLaw Document Management System (DMS), making it searchable, retrievable, and accessible through the relevant Matter or Party record.

### Notes

- The upload ticket and pre-signed URL are time-limited and should be used promptly.
- The uploaded file is not visible within the DMS until the final POST request is made.
- Additional conversion formats may be supported in future releases.

## Document Downloads

The process for downloading documents is:

1. Retrieve a document listing from a party or matter (`GET /parties/{partyId}/documents`).
2. Read the `contentUrl` from the document object you want to download.
3. Perform a simple `GET` request to that URL and include the standard `Authorization` header
4. The response will contain:
    - The raw bytes of the file in the response body  
    - A `Content-Type` header (e.g., `application/pdf`)  
    - A `Content-Disposition` header specifying the recommended filename  

Integrators should treat the `contentUrl` field as opaque and must not construct or modify download URLs.
Always use the exact URL returned with each document. The URL may change over time, may include query parameters or signatures, and may be time-limited. The response may include HTTP redirects, which you should follow automatically.

## Linking Matters to Your Workspace

External links allow integrators to create connections between OneLaw matters and workspaces in external systems. These links appear within the OneLaw application, displaying a URL and optional status indicator that users can click to navigate directly to the integrator's workspace.

To create an external link, use `POST /matters/{matterId}/external-links` with a request body containing:
- URL: The web address of your workspace (required)
- Status: A vendor-specific status indicator (optional)

The response includes the created link with a unique ID and a `Location` header you can use for subsequent operations.

Multiple external links can exist for a single matter, enabling integration with different systems. Each integrator can only see and manage the external links they create. Links created by other integrators are not visible to you. The status field can be updated to reflect workflow states in your system (e.g., "Active", "Pending", "Completed").

## Finding Related Parties

The related-contacts endpoint allows you to discover relationships between parties in OneLaw. You can supply multiple party IDs and receive back a list of relationships that OneLaw has identified between those parties and other parties in the system.

Use `GET /parties/related-contacts` with one or more `partyId` query parameters:

```
GET /parties/related-contacts?partyId={partyId1}&partyId={partyId2}
```

The response contains an array of relationship objects, each describing a connection between:
1. **Known party**: One of the party IDs you provided
2. **Related party**: A party that OneLaw has found to be related to the known party
3. **Relationship**: A human-readable description of how they are related

Each relationship object also includes an optional `relatedMatterId` if the relationship is related to a legal matter—this ID will be present when the connection between parties exists in the context of a specific matter.

### Example: Company Director

For example, if you query with a companies party ID, you might receive:

```
{
  "items": [
    {
      "knownPartyId": "550e8400-e29b-41d4-a716-446655440000",   # party ID of the the company
      "relatedPartyId": "660e8400-e29b-41d4-a716-446655440001", # party ID of the director
      "relationship": "Director",
      "relatedMatterId": null
    }
  ]
}
```

In this example, the known party (the company) is related to another party (the director), and the relationship description explains the connection. If you had also included the director's party ID in your query, you might receive the reverse relationship showing the company. 

### Example: Matter Billing Party

```
{
  "items": [
    {
      "knownPartyId": "550e8400-e29b-41d4-a716-446655440000",   # party ID of a client
      "relatedPartyId": "660e8400-e29b-41d4-a716-446655440001", # party ID of the billing party
      "relationship": "Billing Party",
      "relatedMatterId": "82938500-e29b-41d4-aa75-446655449902"  # matter ID of the matter involved
    }
  ]
}
```

In this example, the known party (the client) has a legal matter with the firm that is being billed to the related party (the billing party). 

## Webhooks (BETA)

You can subscribe to receive webhook notifications for party, matter and document changes in OneLaw Cloud. In the OneLaw App go to Administration > Inegrations > Webhook Subscriptions. Add a new subscription record with the URL that you want to receive the webhook notification.

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
   - `id` - copy of the `webhook-id` header
   - `type` - the type of the event (e.g. "party.created"), determines the schema of the `data` object
   - `timestamp` - timestamp for when the HTTP request was sent in ISO 8601 UTC
   - `data` - the data associated with the event, schema determined by the `type` field
1. The schema for the JSON body content is defined in the OAS / Swagger spec, in the following schema:
   - `PartyWebhookRequest`
   - `MatterWebhookRequest`
   - `DocumentWebhookRequest`

## Next Steps

[Reference](/api/reference/)
