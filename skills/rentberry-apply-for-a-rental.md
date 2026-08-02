---
name: Apply for a rental on Rentberry
description: Build an applicant record with proof-of-income attachments, submit it, apply to a listing, and track the application through acceptance or withdrawal.
api: openapi/rentberry-openapi.yml
operations:
  - post_api_v1_auth_token
  - post_api_v1_applicant_create
  - post_api_v1_applicant_attachment_create
  - post_api_v1_applicant_edit
  - put_api_v1_applicant_submit
  - get_api_v1_applicant_last
  - post_api_v1_apartment_apply_add
  - get_api_v1_apartment_apply_list
  - get_api_v1_apartment_apply_get
  - post_api_v1_apartment_apply_edit
  - delete_api_v1_apartment_apply_decline
generated: '2026-08-02'
method: generated
source: openapi/rentberry-openapi.yml
---

# Apply for a rental on Rentberry

Base URL `https://api.rentberry.com`, paths templated `/v{version}/...`. This is the tenant side of the
Applications surface (17 operations).

## Steps

1. **Authenticate** — `post_api_v1_auth_token`
   `POST /v1/auth/token` with `{"username": "<email>", "plainPassword": "<password>"}`. Returns
   `{"auth_token": "…"}`. A `1005` response means the user is blocked or unverified — that is a Rentberry
   application code, not an HTTP status. Email OTP is an alternative: `post_api_v1_auth_email_otp_request`
   then `post_api_v1_auth_email_otp_verify`.

2. **Create the applicant record** — `post_api_v1_applicant_create`
   `POST /v1/applicant`. Retrieve the current one later with `get_api_v1_applicant_last`, or list them with
   `get_api_v1_get_applicants_list`.

3. **Attach proof of income** — `post_api_v1_applicant_attachment_create`
   `POST /v1/applicant/proof-income`, body `ApplicantAttachmentType`. Returns `201` with an
   `ApplicantAttachment`; `400` means validation failed. Amend with
   `post_api_v1_applicant_attachment_edit`, remove with `delete_api_v1_applicant_attachment_delete`,
   retrieve the file with `get_api_v1_applicant_attachment_download`.

4. **Revise then submit** — `post_api_v1_applicant_edit` to edit, then `put_api_v1_applicant_submit`
   (`PUT /v1/applicant/{id}`) to submit for review. Submit once — there is no idempotency key.

5. **Apply to a listing** — `post_api_v1_apartment_apply_add`
   `POST /v1/apartment/apply`. Rentberry's differentiator is the custom offer, so the application carries
   the tenant's proposed terms rather than only an acceptance of the asking rent.

6. **Track it** —
   - your applications: `get_api_v1_apartment_apply_list` (`GET /v1/apartment/apply`, supports `deleted`,
     `limit`)
   - one application: `get_api_v1_apartment_apply_get`
   - edit: `post_api_v1_apartment_apply_edit`
   - withdraw: `delete_api_v1_apartment_apply_decline`

7. **Landlord side** — the homeowner reads applications for a listing with
   `get_api_v1_apartment_apply_by_apartment` and accepts with `put_api_v1_apartment_apply_accept`
   (`PUT /v1/apartment/apply/accept/{id}`).

## Do not use

`post_api_v1_apartment_apply_confirm` (`POST /v1/apartment/apply/confirm/{id}`) is flagged
`deprecated: true` — mobile-only legacy. See `lifecycle/rentberry-lifecycle.yml`.

## Error handling

Custom envelope `{"body": null, "error": {"code", "message", "description"}}`. `401` = authentication
failed, `403` = "Not enough rights" (the response you get with no token at all), `400` = validation failed.
