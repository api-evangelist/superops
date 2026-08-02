---
name: Manage SuperOps clients and invoices
description: List and create MSP clients and their invoices via the SuperOps MSP GraphQL API, with correct auth, pagination, and error handling.
api: https://developer.superops.com/msp
method: generated
source: https://support.superops.com/en/collections/3666305-api-documentation
operations: [getClientList, createClient, getInvoiceList, createInvoice]
---

# Manage SuperOps clients and invoices

Operating instructions for an agent using the SuperOps MSP GraphQL API to manage clients and billing. Every operation named here is a real documented GraphQL operation.

## Endpoint & authentication

- POST GraphQL to `https://api.superops.ai/msp` (US) or `https://euapi.superops.ai/msp` (EU).
- Send `Authorization: Bearer <API_TOKEN>` and `CustomerSubDomain: <subdomain>` on every request.
- Dates are UTC / ISO 8601. Keep total traffic under 800 requests/minute.

## Steps

1. **List clients** — call `getClientList` with a `ListInfoInput` (`page`, `pageSize` ≤ 100); page with `hasMore` / `totalCount`. Filter with `{ attribute, operator, value }` conditions.
2. **Create a client** — call `createClient`; a duplicate returns `unique_validation_failed`, a missing required field returns `mandatory_validation_failed`.
3. **List invoices** — call `getInvoiceList` for a client, paging the same way.
4. **Create an invoice** — call `createInvoice` referencing an existing client; an unknown reference returns `referred_value_does_not_exist`.

## Error handling

Inspect `errors[].extensions.clientError[].code`. Common codes: `forbidden`, `rate_limit_exceeded`, `entity_does_not_exist`, `referred_value_does_not_exist`, `unique_validation_failed`, `type_validation_failed`, `mandatory_validation_failed`. For bulk mutations, `param.index` identifies which input object failed. See `errors/superops-error-codes.yml`.
