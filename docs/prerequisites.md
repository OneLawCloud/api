---
title: Prerequisites
permalink: /prerequisites/
nav_order: 20
---

# Prerequisites

> ⚠️ **Draft:** This page is a work in progress and may change.

## Getting Access

Access to the OneLaw Cloud API is available only to approved partners enrolled in the OneLaw Partnership Program.
This program ensures that integrations meet OneLaw's technical, security, and compliance standards, and that data is accessed only with appropriate authorization from participating law firms.

To begin the enrollment process, please contact <support@onelaw.co.nz>.
Our team will guide you through the partnership onboarding steps and provide the information needed to begin development against the OneLaw Cloud API.

## Test Tenant

Once your partnership request is approved, OneLaw will provision a test tenant for your organization.
This tenant simulates a complete law firm environment within our cloud platform and is intended exclusively for development and testing purposes.

Your test tenant allows you to:

- Create and manage users
- Enable and configure API access
- Create and modify test data
- Perform full integration and authorization flows

Our team will set up this environment for you and provide the necessary connection details once provisioning is complete.

## Credentials

You will also receive a Client ID and Client Secret. These credentials are used to initiate OAuth 2.0 Authorization Code Flow sign-ins for users.
Once a user has signed in, your application can exchange the authorization code for an access token, which grants secure, scoped access to the API on behalf of that user.

## Enabling API Access

After you receive your test tenant credentials, you'll need to enable API access in the OneLaw Practice Management application:

- Open the OneLaw Practice Management app (Windows only).
- Go to Administration → Integration.
- Turn on the Public API setting

Add an Authorized App using the Client ID assigned to you by OneLaw.

When the Public API is enabled and your application is authorized, the tenant will begin accepting authenticated API requests from your integration.

## Next Steps

[Authentication](/api/authentication/)
