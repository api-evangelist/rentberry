---
name: Screen a tenant on Rentberry
description: Create a screening request, validate the tenant's data, run the screen, and retrieve the credit and criminal background reports.
api: openapi/rentberry-openapi.yml
operations:
  - post_api_v1_screening_request_create
  - get_api_v1_screening_request_list
  - get_api_v1_screening_request_get
  - post_api_v1_screening_request_resend
  - post_api_v1_screening_validate_screening_data
  - post_api_v1_screening_screen_user
  - get_api_v1_reports_preview
  - get_api_v1_credit_score_report
  - get_api_v1_criminal_report
  - delete_api_v1_screening_request_cancel
generated: '2026-08-02'
method: generated
source: openapi/rentberry-openapi.yml
---

# Screen a tenant on Rentberry

Base URL `https://api.rentberry.com`, paths templated `/v{version}/...`. Homeowner-initiated screening.
US-only per Rentberry's pricing page ("Credit and background check (US only) $39.99"). Requires a token
from `post_api_v1_auth_token`.

## Handle this data carefully

Credit scores and criminal background reports are consumer-report data. Do not cache, log or forward the
report bodies. Two of the three report operations return **HTML**, not JSON.

## Steps

1. **Create the request** — `post_api_v1_screening_request_create` (`POST /v1/screening`). The tenant is
   invited by email; resend with `post_api_v1_screening_request_resend`
   (`POST /v1/screening/resend/{id}`).

2. **Track requests** — `get_api_v1_screening_request_list` (`GET /v1/screening`, supports `active`,
   `page`, `limit`) and `get_api_v1_screening_request_get` (`GET /v1/screening/{reference}`). Note the
   mixed key: the list/cancel operations key on `{id}`, the read/validate/screen operations key on
   `{reference}`.

3. **Validate the tenant's data** — `post_api_v1_screening_validate_screening_data`
   (`POST /v1/screening/validate/{reference}`) before spending on the screen.

4. **Run the screen** — `post_api_v1_screening_screen_user`
   (`POST /v1/screening/screen/{reference}`).

5. **Read the results** —
   - `get_api_v1_reports_preview` (`GET /v1/screening/reports-preview/{id}`) — summary preview
   - `get_api_v1_credit_score_report` (`GET /v1/screening/credit-score/{id}`) — credit score report, HTML
   - `get_api_v1_criminal_report` (`GET /v1/screening/criminal-report/{id}`) — criminal background, HTML

6. **Cancel** — `delete_api_v1_screening_request_cancel` (`DELETE /v1/screening/{id}`).

## Do not use

`post_api_v1_screening_screen_pay` (`POST /v1/screening/screen/{reference}/pay`) is flagged
`deprecated: true`; its own description says to use the Stripe webhook flow instead.

## Related

Identity verification is a separate surface: `post_api_v1_user_verify` (US, IDology-backed, with
`post_api_v1_user_verify_answers` for the knowledge questions) and `post_api_v1_user_verify_init` /
`post_api_v1_user_verify_by_documents` / `get_api_v1_user_verify_document_status` (non-US, document based).
