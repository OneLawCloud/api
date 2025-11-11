---
title: Data Model
permalink: /data-model/
nav_order: 45
---

# Data Model

> ⚠️ **Draft:** This page is a work in progress and may change.

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

Parties can have zero or more Party Roles, depending on their relationship with the law firm.

Role | Description
-----|------------
None | Many parties have no roles and are essentially just a named contact - for example, counter-parties on matters or billing contacts.
Client | Parties who are clients of the law firm have this role, which grants them access to legal matters, time recording, billing, and related functions.
User | Law firm staff members are also represented as Parties, each with a User role.

## Next Steps

[Using the API](/api/using-the-api/)
