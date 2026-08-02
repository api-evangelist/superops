---
name: Manage SuperOps service desk tickets
description: List, read, create, and update service desk tickets via the SuperOps MSP GraphQL API, with correct auth, pagination, and error handling.
api: https://developer.superops.com/msp
method: generated
source: https://support.superops.com/en/collections/3666305-api-documentation
operations: [getTicketList, getTicket, createTicket, updateTicket]
---

# Manage SuperOps service desk tickets

Operating instructions for an agent using the SuperOps MSP GraphQL API to work with tickets. Every operation named here is a real documented GraphQL operation.

## Endpoint & authentication

- POST GraphQL to the MSP endpoint for your data center: `https://api.superops.ai/msp` (US) or `https://euapi.superops.ai/msp` (EU).
- Send both headers on every request:
  - `Authorization: Bearer <API_TOKEN>` — token from Settings > My Profile > API token tab.
  - `CustomerSubDomain: <subdomain>` — from Settings > MSP Information.
- Use UTC and ISO date-time (e.g. `2022-04-10T10:15:30`). Stay under 800 requests/minute.

## Steps

1. **List tickets** — call `getTicketList` with a `ListInfoInput` (`page`, `pageSize` ≤ 100). Read `totalCount` / `hasMore` to page; never request more than 100 records at once. Filter with `{ attribute, operator, value }` conditions (e.g. `status is Open`), combining with `joinOperator` AND/OR.
2. **Read one ticket** — call `getTicket` with the ticket id to fetch full detail before mutating.
3. **Create a ticket** — call `createTicket` with the required fields; a missing required field returns `mandatory_validation_failed` and a type mismatch returns `type_validation_failed`.
4. **Update a ticket** — call `updateTicket` with the id and changed fields.

## Error handling

Responses carry GraphQL `errors[].extensions.clientError[]` with a `code`. Handle: `forbidden` (token lacks authorization), `rate_limit_exceeded` (back off below 800/min), `entity_does_not_exist`, `unique_validation_failed`, and the validation codes above. A 401 means the API token is invalid/deleted; a 400 means the CustomerSubDomain is wrong. See `errors/superops-error-codes.yml`.
