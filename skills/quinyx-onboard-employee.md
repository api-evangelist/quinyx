---
name: Onboard an employee in Quinyx
description: Authenticate, create an organizational group if needed, create an
  employee, and assign them a role using the Quinyx API v3.
api: openapi/quinyx-api-v3-openapi.yml
operations: [getToken, getGroups, createGroup, createEmployee, getRoles, createRoleAssignment]
---

# Onboard an employee in Quinyx

Use the Quinyx API v3 to add a new frontline employee and give them a role.

## Auth
1. Obtain a bearer token — `getToken` (`POST /oauth/v3/token`) with
   `grant_type=client_credentials`, your Client ID/Secret, and the scopes
   `organization:groups:read organization:groups:create hr:employees:create organization:roles:read hr:role-assignments:create`.
   Send it as `Authorization: Bearer <token>` on every call. Tokens last ~3600s.
2. Pick the regional host: `https://api.eu.quinyx.com` or `https://api.na.quinyx.com`.

## Steps
1. Find the target org unit — `getGroups` (`GET /organization/v3/groups`). If it
   does not exist, create it with `createGroup` (`POST /organization/v3/groups`).
2. Create the employee — `createEmployee` (`POST /hr/v3/employees`) with their
   personal information, employment, and contact details.
3. Look up the role to assign — `getRoles` (`GET /organization/v3/roles`).
4. Assign the role — `createRoleAssignment` (`POST /hr/v3/role-assignments`)
   binding the employee, role, and group.

## Rules
- Errors are RFC 9457 `application/problem+json`; branch on the `type` URI
  (see errors/quinyx-problem-types.yml). A `validation-error` (422) means the
  request body failed validation — inspect the error detail.
- Lists are cursor-paginated: pass `pageToken`, read `pagination.nextPageToken`.
- All timestamps are UTC RFC-3339. Rate limit is per-customer; on `429` back off
  and retry.
