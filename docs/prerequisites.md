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

### First-Time Use

You will manage your test tenant using the OneLaw Practice Management app.
The app requires a Windows 10 or later PC with a Full HD (FHD) screen resolution and an internet connection.

1. Download and run the installer from the URL provided.  
2. Launch the OneLaw app from the Start menu.  
3. Click Login.  
4. Enter your email address and select Forgot Password.  
5. Complete email verification and set a password.  
6. Set up mandatory two-factor authentication (2FA).  
7. The OneLaw Practice Management app will load.

### Enabling API Access

After logging in to the OneLaw app, you must enable API access before using it:

1. Open the OneLaw Practice Management app (Windows only).  
2. Go to Administration → Integration.  
3. Turn on the Public API setting.  

Add an Authorized App using the Client ID assigned to you by OneLaw.

When the Public API is enabled and your application is authorized, the tenant will begin accepting authenticated API requests from your integration.

## OAuth Credentials

You will receive a Client ID and Client Secret. These credentials are used to initiate OAuth 2.0 Authorization Code Flow sign-ins for users.  
After a user signs in, your application exchanges the authorization code for an access token, which grants secure, scoped access to the API on behalf of that user.

> 🔒 **Note:** Store the Client Secret securely and never embed it in public or client-side code. Treat these credentials as confidential.
## Next Steps

[Authentication](/api/authentication/)
