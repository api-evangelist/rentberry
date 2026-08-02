---
name: Search Rentberry listings and send an inquiry
description: Find long-term rental listings by place or map bounds, read the details, save the search, and send an inquiry to the landlord.
api: openapi/rentberry-openapi.yml
operations:
  - get_api_v4_search_filter
  - post_api_v4_apartment_search_init
  - post_api_v4_apartment_search_place
  - post_api_v4_apartment_search_bounds
  - get_api_v4_apartment_view
  - get_api_v1_apartment_search_similar_apartments
  - post_api_v4_save_search
  - post_api_v1_apartment_favorite_add
  - post_api_v1_listing_inquiry_create
generated: '2026-08-02'
method: generated
source: openapi/rentberry-openapi.yml
---

# Search Rentberry listings and send an inquiry

Base URL `https://api.rentberry.com`. Every path is templated `/v{version}/...` where `version` is an
integer you supply — use the version stated in the operation description (`Available since API version N`)
or higher. The document's `info.version` is `4`.

## Before you start

- Authenticated operations send an opaque token issued by `post_api_v1_auth_token`. The OpenAPI applies a
  security requirement named `XAuthToken` to 122 operations but never defines the scheme, so confirm the
  exact header name with Rentberry before wiring it (see `authentication/rentberry-authentication.yml`).
- There is no idempotency key. Do not blind-retry the write steps below.
- Errors come back as `{"body": null, "error": {"code": …, "message": …, "description": …}}`, not RFC 9457.
  See `errors/rentberry-problem-types.yml`.

## Steps

1. **Get the filter configuration** — `get_api_v4_search_filter`
   `GET /v4/search-filter-config/{countryCode}/{transactionType}`. Returns the filters valid for that
   country and transaction type. Do not guess filter names; read them from here.

2. **Initialize the search** — `post_api_v4_apartment_search_init`
   `POST /v4/apartment/search/init`.

3. **Run the search** — pick one:
   - by place: `post_api_v4_apartment_search_place` — `POST /v4/apartment/search/place`
   - by map bounds: `post_api_v4_apartment_search_bounds` — `POST /v4/apartment/search/bounds`
   Mobile clients have separate operations (`post_api_v2_apartment_search_place_mobile`,
   `post_api_v2_apartment_search_bounds_mobile`, `post_api_v2_apartment_search_nearby`).
   Do not use `post_api_v1_apartment_search_buckets` — it is marked deprecated.

4. **Read a listing** — `get_api_v4_apartment_view`
   `GET /v4/apartment/{id}/view` for the public view. `getListingV3` (`GET /v3/apartment/{id}`) returns the
   v3 detail shape for owners.

5. **Widen the shortlist** — `get_api_v1_apartment_search_similar_apartments`
   `GET /v1/apartment/search/similar-apartment/{id}` with `page` and `limit`.

6. **Persist intent** (authenticated):
   - save the criteria: `post_api_v4_save_search` — `POST /v4/saved-search`
     (disable later with `post_api_v4_disable_save_search`)
   - favourite a listing: `post_api_v1_apartment_favorite_add`, or several at once with
     `post_api_v4_listing_favorite_add_bulk`

7. **Contact the landlord** — `post_api_v1_listing_inquiry_create`
   `POST /v1/listing/{listing}/inquiry`. This operation is rate limited: it declares a `429` response with
   the body `"Too many requests from one IP"`. Back off on 429; there is no `Retry-After` header. Check
   `get_api_v4_listing_inquiry_info` first to see whether an inquiry already exists.

## Pagination

List reads take `page` and `limit` query parameters. `get_api_v1_messages_get_conversation` uses a
`before` cursor instead. No pagination envelope or `Link` header is declared.
