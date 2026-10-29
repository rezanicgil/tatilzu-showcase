# Tatilzu — architecture overview

[Back to the showcase](../README.md)

This document describes the application's main boundaries, based on the project structure and the public demo reviewed on 29 October 2026. It intentionally omits credentials, deployment identifiers and internal session links.

## Request and data flow

1. A Cloudflare Worker serves the Vite-built React application and handles API routes.
2. The browser applies search and offer-matching logic to a typed fictional hotel catalogue. Trip parameters determine illustrative totals.
3. Firebase handles client sign-in. Authenticated API calls carry an identity token; the Worker verifies it before account-scoped operations.
4. D1 holds persistent account and saved-list data. Browsing demo fixtures does not require access to an account database.
5. MapLibre loads on demand. Map resources are served through the Worker from R2-backed map storage.

## Boundaries and trade-offs

| Decision | Benefit | Constraint |
| --- | --- | --- |
| Typed local catalogue for the demo | Repeatable filtering and pricing behavior; no supplier dependency for browsing. | Does not represent current inventory, availability or market prices. |
| One Worker for assets and API | A compact deployment boundary with shared routing and headers. | API behavior still needs independent validation, monitoring and capacity assessment. |
| D1 for relational persistence | Account-scoped saved data with schema migrations. | Query patterns and load limits must be measured as usage grows. |
| R2 for map storage | Large map datasets stay outside relational records and frontend bundles. | Map caching, delivery costs and attribution still require attention. |
| Lazy-loaded map UI | The discovery page does not immediately require the map renderer. | Loading and failure states are part of the interface contract. |
| Public showcase, private application | Product and engineering decisions can be reviewed without publishing implementation history. | Visitors cannot clone and run the application from this repository. |

## Correctness and reliability

**Offer consistency.** A hotel's payment, board and cancellation attributes are evaluated against a matching offer. A filter must not combine the price of one offer with the cancellation policy of another.

**Trip context.** Routes and sharing helpers preserve relevant travel parameters. Pricing in the demo is deterministic, not a live supplier quotation.

**Identity boundary.** Account operations depend on server-side token verification and account ownership. Public catalogue browsing and authenticated persistence are separate concerns.

**HTTP behavior.** The API implementation includes health handling, origin checks, controlled error responses and distinct public/private cache behavior. Request limiting is configurable; its presence in code is not a claim that every deployed environment has identical limits.

**Environment isolation.** Staging and production have separate configuration and data boundaries. A successful frontend build is not a production release or a completed provider integration.

## Validation strategy

The application repository contains regression tests around filtering, demo totals, routes, sharing, authentication and Worker responses. Browser review complements those checks for responsive layout, map interactions, empty/error states and accessibility behavior.

This documentation update did not run the application's complete test suite or a production load test. It does not claim measured throughput, an uptime SLA or a coverage percentage.

## Beyond the demo

- Validate supplier permissions, attribution, caching terms and data freshness before enabling real hotel inventory.
- Instrument error rates, request latency, database operations and external dependency failures; define actionable alerts before claiming production SLOs.
- Test representative query patterns and traffic, then tune caching and persistence based on measurements.
- Evaluate AI-assisted research separately, including source attribution, cost budgets and failure behavior. The public screenshots demonstrate the deterministic discovery experience.

## Public documentation boundary

The showcase contains no application source, environment files, database exports, authentication keys or Git history from the private repository. Only documentation and reviewed product screenshots are published here.
