---
name: Read an employee's schedule
description: Authenticate and read shifts and employee records from the Quinyx
  API v3 to build a schedule view.
api: openapi/quinyx-api-v3-openapi.yml
operations: [getToken, getEmployees, getEmployee, getShifts]
---

# Read an employee's schedule

Use the Quinyx API v3 to retrieve employees and their scheduled shifts.

## Auth
1. `getToken` (`POST /oauth/v3/token`), `grant_type=client_credentials`, scopes
   `hr:employees:read schedule:shifts:read`. Use `Authorization: Bearer <token>`.
2. Regional host: `https://api.eu.quinyx.com` or `https://api.na.quinyx.com`.

## Steps
1. List employees — `getEmployees` (`GET /hr/v3/employees`), page with
   `pageToken` until `pagination.nextPageToken` is empty.
2. (Optional) Fetch one employee — `getEmployee` (`GET /hr/v3/employees/{id}`).
3. List shifts — `getShifts` (`GET /schedule/v3/shifts`), filtering to the
   employee/group and time window; page with `pageToken`.

## Rules
- Cursor pagination: default page size 50, max 100; treat `pageToken` as opaque.
- All times are UTC RFC-3339; send a timezone header to localize.
- Errors are RFC 9457 problem+json (errors/quinyx-problem-types.yml).
- Read scopes only — this flow never mutates data.
