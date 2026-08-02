# Rentberry

Rentberry, Inc. is an international long-term home rental marketplace founded in 2015 and headquartered in
San Francisco, California. The platform runs the whole rental lifecycle in one closed loop for tenants and
landlords: listing and syndication, worldwide search, virtual tours and open houses, custom rent offers,
applications with proof of income, US credit and background screening, e-signed contracts and contract
templates, Stripe-backed rent collection and rental subscriptions, messaging, maintenance and complaints.

- Website: https://rentberry.com/
- API reference (Swagger UI): https://api.rentberry.com/docs
- Help center: https://help.rentberry.com/en/
- GitHub: https://github.com/Rentberry

## APIs

| API | Contract | Surface |
|---|---|---|
| Rentberry API | OpenAPI 3.0.0 — `openapi/rentberry-openapi.yml` | 188 paths, 220 operations, 44 tags, 164 schemas |
| Rentberry Geocoder | proto3 — `grpc/rentberry-geocoder.proto` | GeocodeService, TimezoneService |

The OpenAPI was harvested on 2026-08-02 from the Swagger UI at `https://api.rentberry.com/docs`, where it is
embedded in the page as `<script id="swagger-data" type="application/json">`. There is no standalone
`/openapi.json` or `/swagger.json` endpoint. The unmodified document is kept at
`openapi/_original/rentberry-openapi.json`.

## Artifacts

`openapi/` `grpc/` `authentication/` `conventions/` `errors/` `lifecycle/` `conformance/` `data-model/`
`overlays/` `packages/` `sandbox/` `security/` `well-known/` `llms/` `mcp/` `skills/`

## Notable gaps (probed 2026-08-02)

- OpenAPI declares no `servers[]` and no `components.securitySchemes`, yet 122 operations reference a
  scheme named `XAuthToken`; 22 operations are untagged. See `overlays/rentberry-openapi-overlay.yaml`.
- No idempotency contract on any write, including payments, subscriptions and payouts.
- No `/.well-known/` documents, no A2A agent card, no MCP server, no GraphQL.
- No consumer webhooks or AsyncAPI — the webhook paths in the spec are inbound Stripe / Mailchimp /
  Mandrill receivers Rentberry runs for itself.
- No client SDK for the API in any package registry.
- No status page, changelog, deprecation policy or SLA.
- No vulnerability disclosure program, trust center or published certifications, despite handling US
  consumer screening data and card payments.
