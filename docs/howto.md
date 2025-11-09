---
title: How-To
permalink: /how-to/
nav_order: 60
---

# How-To

> ⚠️ **Draft:** This page is a work in progress and may change.

## Checking if the API Is Available

Before making any API calls, you should confirm that the API is supported and reachable for the signed-in user's tenant.

Once you have a signed-in user and the base URL from the config service, send a GET request to the /ping endpoint:

GET https://firm123.api.onelaw.cloud/v1/ping
Authorization: Bearer <access_token>

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

    Begin by calling GET /uploads/ticket.
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

4. Store the Document in the DMS
    
    Once the file (and any conversion) is complete, associate it with a specific record by posting to one of the following endpoints:

    `POST /matters/{matterId}/documents`

    `POST /parties/{partyId}/documents`

    In the request body, include:

    The uploadId from step 1

    Metadata such as document name, type, and description

    This final step registers the document within the OneLaw Document Management System (DMS), making it searchable, retrievable, and accessible through the relevant Matter or Party record.

Notes

The upload ticket and pre-signed URL are time-limited and should be used promptly.

The uploaded file is not visible within the DMS until the final POST request is made.

Additional conversion formats may be supported in future releases.






## Linking Matters to Your Workspace













Create and adjust a time entry – constraints (6-minute increments) and validation examples. 

Presigned upload flow: get upload URL, PUT bytes, attach to Document, optional conversion to PDF. 

Link a Matter to an external workspace – creating and reading External Links, typical statuses. 

Search for related contacts – how to supply known party IDs and interpret relationships. 

Health checks – Status/Ping endpoint usage for monitoring. 

## Next Steps

[Reference](/api/reference/)
