---
name: Collect rent on Rentberry
description: Set up a rental, onboard the landlord's payment account, create a Stripe-backed rental subscription, take payments, and pay out the balance.
api: openapi/rentberry-openapi.yml
operations:
  - post_api_v1_rental_create
  - post_api_v1_rental_invite_tenant
  - get_api_v1_rental_activate
  - post_api_v1_payments_onboarding_create_account
  - post_api_v1_payments_onboarding_finish
  - post_api_v4_rental_subscription_stripe
  - get_api_v4_rental_subscription_stripe_get
  - post_api_v4_rental_subscription_stripe_update
  - delete_api_v4_rental_subscription_stripe_cancel
  - post_api_v1_rental_payment_stripe
  - get_api_v1_rental_payment_list_for_rental_stripe
  - get_api_v1_rental_payment_account
  - post_api_v1_rental_payment_account_create_payout
generated: '2026-08-02'
method: generated
source: openapi/rentberry-openapi.yml
---

# Collect rent on Rentberry

Base URL `https://api.rentberry.com`, paths templated `/v{version}/...`. Requires a token from
`post_api_v1_auth_token`. Money movement runs through Stripe; rent collection is a paid landlord feature
($19.99 per Rentberry's pricing page).

## Money-safety rules

**There is no idempotency key anywhere in this API.** Every payment, subscription and payout operation
below is unsafe to blind-retry. Before retrying a write, re-read state with
`get_api_v1_rental_payment_list_for_rental_stripe` or `get_api_v4_rental_subscription_stripe_get` and
confirm the prior attempt did not land.

## Steps

1. **Create the rental** — `post_api_v1_rental_create` (`POST /v1/rental`). Update with
   `post_api_v1_rental_update`, archive with `post_api_v1_rental_archive`. Attach documents with
   `post_api_v1_rental_add_document` and read them back with `get_api_v1_rental_download_document`.

2. **Bring in the counterparty** — `post_api_v1_rental_invite_tenant`
   (`POST /v1/rental/invite-tenant/{rental}`) or `post_api_v1_rental_invite_homeowner`. The tenant
   activates with `get_api_v1_rental_activate` (`GET /v1/rental/activate/{reference}`).

3. **Onboard the payment account** — `post_api_v1_payments_onboarding_create_account`
   (`POST /v1/payments/onboarding/account`), then `post_api_v1_payments_onboarding_finish`.

4. **Recurring rent** — `post_api_v4_rental_subscription_stripe`
   (`POST /v4/rental/{rental_id}/subscription/stripe`). Read it with
   `get_api_v4_rental_subscription_stripe_get`, change it with
   `post_api_v4_rental_subscription_stripe_update`, cancel with
   `delete_api_v4_rental_subscription_stripe_cancel`.

5. **One-off payments** — `post_api_v1_rental_payment_stripe`
   (`POST /v1/rental/{rental_id}/payment/stripe`); list with
   `get_api_v1_rental_payment_list_for_rental_stripe`; amend with
   `post_api_v1_rental_payment_stripe_update`; cancel with
   `delete_api_v1_rental_payment_stripe_cancel`.

6. **Landlord balance and payouts** — `get_api_v1_rental_payment_account`
   (`GET /v1/rental/payment/account/balance`) then `post_api_v1_rental_payment_account_create_payout`
   (`POST /v1/rental/payment/account/payout`).

7. **Chargeable rentals** — `get_api_v1_chargeable_rentals_list`
   (`GET /v1/rental/chargeable/{userType}`) lists rentals with money owing.

## Events

`post_api_v1_stripe_direct_webhook` and `post_api_v1_stripe_connected_webhook` are Rentberry's own inbound
Stripe receivers. They are not a webhook subscription you can register for — Rentberry publishes no
consumer event surface, so reconcile by polling the list operations above.
