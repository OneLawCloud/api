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

## Time Entries

Time entries record billable or non-billable time worked on legal matters by users.

### Time Increments

Time entries are always expressed in **minutes** and must start and stop at times that align with **6-minute increments from the start of each hour**.

Valid start and stop times are, e.g.:
- 00:00, 00:06, 00:12, 00:18, 00:24, 00:30, 00:36, 00:42, 00:48, 00:54
- And so on for each hour...

This means time entries must begin and end at one of these 6-minute boundaries (every 0.1 hours). Any time entry that does not align with these boundaries will be rejected with a validation error.

## Next Steps

[Using the API](/api/using-the-api/)
