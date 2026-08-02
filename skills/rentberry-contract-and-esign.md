---
name: Create and e-sign a Rentberry rental contract
description: Build a contract from a template, publish it, invite the counterparty, collect signatures, and download the signed document.
api: openapi/rentberry-openapi.yml
operations:
  - get_api_v1_contract_template_list
  - post_api_v1_contract_template_create
  - post_api_v1_contract_create
  - post_api_v1_contract_publish
  - post_api_v1_contract_invite
  - get_api_v1_contract_sign
  - get_api_v1_contract_sign_by_token
  - get_api_v1_contract_signatures
  - get_api_v1_contract_download
  - get_api_v1_contract_list
  - get_api_v1_contract_rental_list
  - delete_api_v1_contract_delete
generated: '2026-08-02'
method: generated
source: openapi/rentberry-openapi.yml
---

# Create and e-sign a Rentberry rental contract

Base URL `https://api.rentberry.com`, paths templated `/v{version}/...`. Requires a token from
`post_api_v1_auth_token`. E-signing is a paid landlord feature ($19.99 per Rentberry's pricing page).

## Steps

1. **Pick or create a template** — `get_api_v1_contract_template_list` (`GET /v1/contract/template`,
   supports `page`, `limit`); create with `post_api_v1_contract_template_create`, amend with
   `post_api_v1_contract_template_edit`, fetch the file with
   `get_api_v1_contract_template_download`, remove with `delete_api_v1_contract_template_delete`.

2. **Create the contract** — `post_api_v1_contract_create` (`POST /v1/contract`).

3. **Publish it** — `post_api_v1_contract_publish` (`POST /v1/contract/publish/{id}`). Publish once; there
   is no idempotency key, and a duplicate publish is a real state change.

4. **Invite the counterparty** — `post_api_v1_contract_invite` (`POST /v1/contract/invite/{id}`).

5. **Signing** —
   - authenticated signer: `get_api_v1_contract_sign` (`GET /v1/contract/sign/{id}`) returns the signing URL
   - invited signer with a link token: `get_api_v1_contract_sign_by_token`
     (`GET /v1/contract/sign/token/{token}`)
   Treat the token as a bearer credential: it grants signing rights on its own. Never log it.

6. **Poll signature state** — `get_api_v1_contract_signatures` (`GET /v1/contract/{id}/signatures`).
   There is no consumer webhook for signature completion — `post_api_v1_contract_callback`
   (`POST /v1/contract/callback`) is Rentberry's own inbound receiver from its e-sign provider, not an
   event you can subscribe to. Poll instead, and back off: the API declares `429` on several operations.

7. **Retrieve the executed document** — `get_api_v1_contract_download`
   (`GET /v1/contract/download/{id}`).

8. **Browse** — `get_api_v1_contract_list` (`GET /v1/contract`) for signed contracts,
   `get_api_v1_contract_rental_list` (`GET /v1/contract/rental/{id}`) for one rental's contracts.
   `delete_api_v1_contract_delete` removes an unexecuted contract.
