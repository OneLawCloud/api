---
title: Introduction
---

> ⚠️ **Draft:** This page is a work in progress and may change.

The OneLaw Cloud API provides programmatic access to the OneLaw Cloud legal practice management platform. It allows approved partners and integrators to securely interact with a firm’s data — including parties, clients, matters, time entries, and documents — using standard RESTful requests and JSON payloads.

The API is intended for developers building software that integrates with OneLaw Cloud: for example, legal service automation tools, document generation platforms, time-tracking integrations, or client relationship management systems. Access is available only under a partnership agreement, ensuring that all integrations meet OneLaw’s data protection and compliance standards.

## Environments and Base URLs

OneLaw Cloud is a single-tenanted system: each law firm has its own isolated service instance, including its own API endpoint.
There is no single global base URL for the OneLaw Cloud API.

Instead, applications determine the correct base URL for a user or tenant by calling a configuration service. After obtaining an access token through OAuth 2.0, the client calls this configuration endpoint, which returns the base URL for the tenant’s API. All subsequent API requests must be directed to that URL.

## Test tenant

Integrators are provided with a test tenant within the OneLaw Cloud environment. This tenant is a fully functional sandbox where you can develop and test integrations without affecting production data.
When an integration is ready, individual law firms can whitelist that integration in their own tenant, allowing the integration to access firm data through the firm’s dedicated API endpoint.

## Versioning

Because each law firm runs its own dedicated instance of the OneLaw Cloud service, different firms may be running one of several recent versions. Each service version supports a specific OpenAPI specification, which defines the endpoints, schemas, and authentication models supported by that version.

OneLaw Cloud | API Version                | Notes
-------------|----------------------------|------
4.2.4        | [1.0.0](/api/specs/1.0.0/) | Initial public release
4.2.5        | [1.1.0](/api/specs/1.1.0/) | Added resources: firm & matter external links
4.3.0        | [1.2.0](/api/specs/1.2.0/) | In development

A full changelog will be maintained to record new endpoints, parameter additions, and deprecations. New versions are designed to be non-breaking whenever possible, and existing API versions remain supported for a defined period after deprecation. See [Release Notes](releasenotes.md) for more.

## Backward Compatibility Policy

The OneLaw Cloud API follows a semantic versioning model (MAJOR.MINOR.PATCH):

- PATCH versions include non-breaking bug fixes or clarifications in documentation.
- MINOR versions may introduce new endpoints, response fields, or optional parameters, but remain backward compatible.
- MAJOR versions may include breaking changes.

Breaking changes include (but are not limited to):

- Removing or renaming endpoints or paths
- Changing HTTP methods or required parameters
- Altering response structure in a way that breaks existing clients
- Modifying authentication or authorization requirements

When a breaking change is necessary, it will be introduced in a new major version of the API with a new base URL, and with prior versions supported during a deprecation window to give integrators time to upgrade.

## Next Steps

[Prerequisites](prerequisites.md)

[Home](/index.md)
