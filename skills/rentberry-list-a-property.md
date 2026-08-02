---
name: List a property on Rentberry
description: Create a rental listing as a homeowner, upload and order pictures, run amenity detection, promote it, and check syndication eligibility.
api: openapi/rentberry-openapi.yml
operations:
  - post_api_v1_apartment_new
  - post_api_v1_apartment_picture_create
  - post_api_v1_apartment_picture_order_edit
  - post_api_v4_apartment_picture_detect_amenities
  - post_api_v4_apartment_description_stream
  - post_api_v1_apartment_edit
  - get_api_v1_apartment_list
  - post_api_v1_apartment_promote
  - get_api_v1_apartment_is_syndicatable
  - get_api_v1_apartment_get_competitors
  - delete_api_v1_apartment_archive
generated: '2026-08-02'
method: generated
source: openapi/rentberry-openapi.yml
---

# List a property on Rentberry

Base URL `https://api.rentberry.com`, paths templated `/v{version}/...`. Homeowner side of the Listings
surface (29 operations). All steps require a token from `post_api_v1_auth_token`.

## Steps

1. **Create the listing** — `post_api_v1_apartment_new` (`POST /v1/apartment`).

2. **Add pictures** — `post_api_v1_apartment_picture_create` (`POST /v1/apartment/pictures`), then
   `post_api_v1_apartment_picture_order_edit` (`POST /v1/apartment/pictures/order`) to set display order.
   Edit with `post_api_v1_apartment_picture_edit`, remove with `delete_api_v1_apartment_picture_delete`.

3. **Derive amenities from the photos** — `post_api_v4_apartment_picture_detect_amenities`
   (`POST /v4/apartment/pictures/detect-amenities`).

4. **Generate the description** — `post_api_v4_apartment_description_stream`
   (`POST /v4/apartment/description/stream`). This is a streaming response; consume it incrementally rather
   than waiting for a complete JSON body.

5. **Update and review** — `post_api_v1_apartment_edit` (`POST /v1/apartment/{id}`). List your own
   inventory with `get_api_v1_apartment_list` (`GET /v1/apartment`, supports `active`, `page`, `limit`) or
   just the ids with `get_api_v4_listings_ids_list`.

6. **Position it** — `get_api_v1_apartment_get_competitors` (`GET /v1/apartment/{id}/competitors`) returns
   comparable listings; `post_api_v1_apartment_promote` (`POST /v1/apartment/{id}/promote`) buys promotion.
   Promotion and syndication are paid features — check `get_api_v1_feature_view_prices` and
   `get_api_v1_feature_view_ticket` before charging, and pay with
   `post_api_v1_feature_create_stripe_payment`.

7. **Check syndication** — `get_api_v1_apartment_is_syndicatable`
   (`GET /v1/apartment/{id}/syndicatable`).

8. **Handle viewings** — publish open-house slots and read applications with
   `get_api_v1_apartment_tour_applies_by_tennants`; accept with `put_api_v1_apartment_tour_apply_accept`,
   decline with `delete_api_v1_apartment_tour_apply_decline`. `post_api_v1_apartment_tour_apply` is rate
   limited (declares `429`).

9. **Retire it** — `delete_api_v1_apartment_archive` (`DELETE /v1/apartment/{id}`) archives or deletes;
   `put_api_v1_apartment_un_archive` restores.

## Notes

- Writes are not idempotent — no `Idempotency-Key` exists anywhere in the contract. Guard retries yourself.
- Maintenance requests against a listing: `post_api_v1_maintenance_new`
  (`POST /v1/apartment/maintenance/{id}`).
