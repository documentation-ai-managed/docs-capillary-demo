---
title: "API-Reference Migration Verification Report"
description: "Audit of ReadMe (source) vs DocumentationAI (converted) API-Reference prose for 196 pages"
---

# API-Reference Migration Verification Report

Read-only audit comparing every DocumentationAI API-Reference page (`docs-capillary-demo/reference/<slug>.mdx`) against its original ReadMe source. Per the audit rules, content the OpenAPI playground (`api-reference/<slug>.yaml`) already renders — endpoint description, request parameters, request-body schema, sample request/response — is **not** flagged as missing. Only human-readable prose (Resource Information, Prerequisites, notes/callouts, error-code tables, response-parameter description tables, "how it works" narrative, images) is compared.

## 1. Summary counts

| Verdict | Count | Meaning |
|---------|------:|---------|
| **Total pages** | **196** | every leaf `"path": "reference/<slug>"` in `documentation.json` |
| `OK` | 154 | all source prose present in the converted page |
| `MISSING-CONTENT` | 0 | source prose partially dropped (none found) |
| `EMPTY-STUB` | 12 | converted `.mdx` is frontmatter-only, but a local source with real prose exists |
| `NEEDS-REVIEW` | 1 | matched but the correct source is ambiguous |
| `NO-SOURCE` | 29 | no source in any local dir or on the live ReadMe (destination slug 404s) |

**Headline:** every page that was converted *with* content was converted faithfully — no page shows partial prose loss (`MISSING-CONTENT` = 0). All defects are all-or-nothing: 13 pages that have a recoverable local source but were migrated as empty playground-only stubs, plus 29 playground-only stubs whose ReadMe source could not be located.

### How the audit was run (reproducible, non-hallucinated)

- **Slug list:** 196 slugs from `documentation.json` leaves.
- **Source resolution, first match wins:** exact `<slug>.md` in the three source dirs → endpoint `METHOD /path` remap (read from the `.yaml`, grepped across source dirs) → fuzzy filename → live `docs.capillarytech.com/reference/<slug>`.
- **154 exact-source pages** were mechanically diffed on section-heading presence, priority sections (`Resource information` / `Prerequisites` / `Error codes` / `Response parameters`), callout-count parity, prose-table-count parity, and image-count parity. **Zero** dropped prose, callouts, tables, or images. `approve-or-reject-a-request` and `loyalty-promotions` were also hand-checked to confirm the mechanical result.
- **42 pages had no exact-slug source:** 13 remapped to a local source via shared endpoint path / fuzzy filename; 29 had none locally and return `404` on the live ReadMe destination slug (verified by WebFetch on a sample; bulk `curl` checks were rate-limited to `429` but every WebFetch-sampled slug returned a clean `404`).
- The `capillary-docs` MCP indexes the **destination** DocumentationAI site (not ReadMe), and the `Readme` MCP points at `docs.readme.com`; neither could supply original ReadMe prose, so neither was used as a source substitute.

## 2. Per-page results

Only the 42 non-`OK` rows are listed individually below; the 154 `OK` pages are summarised at the end of this table.

| slug | source file used (or live URL) | verdict | missing sections |
|------|--------------------------------|---------|------------------|
| add-promotion-redemption | INFO/backfill/raw/get-cart-promotion-redemptions.md | `NEEDS-REVIEW` | entire page body (converted .mdx is frontmatter-only). Source prose: Example request, Prerequisites, Resource information, Query parameters, Example response, Response parameters, Error codes |
| change-password-1 | INFO/backfill/raw/change-password.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Prerequisite, Resource Information, Request URL, Example request, Request body parameters, Example response |
| evaluate-promotion | INFO/docs.capillarytech.com/API Reference/post_api-gateway-v1-promotions-evaluate.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Example request, Headers, Request Body, Example response, Response parameters, Error codes |
| forget-password-1 | INFO/backfill/raw/forget-password.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Resource Information, Request URL, Example request, Example response |
| generate-authentication-token | INFO/docs.capillarytech.com/API Reference/generateauthtoken.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Request Body parameters, Response Body parameters |
| generate-otp-1-1 | INFO/backfill/raw/generate-otp-api.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Resource Information, Request URL, Request Body Parameters, Error |
| get-custom-fields-associated-with-coupon-redemption | INFO/backfill/raw/get-custom-fields-associated-with-coupon-redemption-global.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Prerequisites, Resource information, API endpoint example, Request query parameters, Sample request, Response parameters, Sample response, Error Codes |
| get-customer-reward-transactions | capillary-migrator/verseion-2-capillary-api-references/get-all-reward-transactions-for-a-user.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Example request, Prerequisites, Resource information, API endpoint example, Request path parameters, Request query parameters, Response parameters |
| issue-user-reward | INFO/backfill/raw/post-issue-user-reward.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Prerequisites, Resource information, API endpoint example, Request query parameters, Request body parameters, Response parameters, Example: Issuing reward with quantity and redemption value, Example: Issuing reward with only quantity, Response parameters, Example: Issuing reward with quantity and redemption value, Example: Issuing reward with only quantity, Example: Issuing reward created for customer segment, API-specific error codes |
| post-revoke-earned-promotion | INFO/docs.capillarytech.com/API Reference/revoke-earned-promotion.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Example request, Prerequisites, Resource information, Request body parameters, Example response, Response parameters, Error codes |
| regenerate-authentication-token-1 | INFO/backfill/raw/regenerate-authentication-token.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Resource Information, Request URL, Example request, Example response, Error code |
| validate-otp-2 | INFO/backfill/raw/validate-otp-api.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Resource Information, Request URL, Request Body Parameters, Sample response, Response parameters |
| validate-password-1 | INFO/backfill/raw/validate-password.md | `EMPTY-STUB` | entire page body (converted .mdx is frontmatter-only). Source prose: Resource Information, Request URL, Request Body Parameters, Response parameters, Error code |
| add-promotion | none — live `docs.capillarytech.com/reference/add-promotion` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| claim-reward | none — live `docs.capillarytech.com/reference/claim-reward` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| create-reward-currency-limits | none — live `docs.capillarytech.com/reference/create-reward-currency-limits` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-allocated-points-details-with-event-id | none — live `docs.capillarytech.com/reference/get-allocated-points-details-with-event-id` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-brand-rewards | none — live `docs.capillarytech.com/reference/get-brand-rewards` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-cart-recommendations | none — live `docs.capillarytech.com/reference/get-cart-recommendations` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-custom-field-for-transaction-add-event | none — live `docs.capillarytech.com/reference/get-custom-field-for-transaction-add-event` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-event-log-ids-for-credit-debit | none — live `docs.capillarytech.com/reference/get-event-log-ids-for-credit-debit` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-event-log-ids-with-credit-or-debit-for-alternate-currency | none — live `docs.capillarytech.com/reference/get-event-log-ids-with-credit-or-debit-for-alternate-currency` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-expired-points-for-a-customer | none — live `docs.capillarytech.com/reference/get-expired-points-for-a-customer` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-item-recommendations | none — live `docs.capillarytech.com/reference/get-item-recommendations` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-ledger-information-for-event-log-ids | none — live `docs.capillarytech.com/reference/get-ledger-information-for-event-log-ids` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-manual-points-adjustment-details | none — live `docs.capillarytech.com/reference/get-manual-points-adjustment-details` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-point-redemption-details-for-redemption-events | none — live `docs.capillarytech.com/reference/get-point-redemption-details-for-redemption-events` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-reward-currency-limits | none — live `docs.capillarytech.com/reference/get-reward-currency-limits` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-target-completion-details | none — live `docs.capillarytech.com/reference/get-target-completion-details` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-user-recommendations | none — live `docs.capillarytech.com/reference/get-user-recommendations` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-user-reward | none — live `docs.capillarytech.com/reference/get-user-reward` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-user-rewards | none — live `docs.capillarytech.com/reference/get-user-rewards` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-user-rewards-merge-details | none — live `docs.capillarytech.com/reference/get-user-rewards-merge-details` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| get-user-specific-reward-by-id | none — live `docs.capillarytech.com/reference/get-user-specific-reward-by-id` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| merge-user-rewards | none — live `docs.capillarytech.com/reference/merge-user-rewards` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| post_loyalty-api-v1-workflows-expjson-programid-eventtype | none — live `docs.capillarytech.com/reference/post_loyalty-api-v1-workflows-expjson-programid-eventtype` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| retrieve-pa-id-for-specified-data-range | none — live `docs.capillarytech.com/reference/retrieve-pa-id-for-specified-data-range` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| retrieve-pointsaward-records-associated-with-a-specific-transaction-id | none — live `docs.capillarytech.com/reference/retrieve-pointsaward-records-associated-with-a-specific-transaction-id` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| set-promotion-settings | none — live `docs.capillarytech.com/reference/set-promotion-settings` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| v1-create-badges-org-meta | none — live `docs.capillarytech.com/reference/v1-create-badges-org-meta` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| v1-get-badges-org-meta | none — live `docs.capillarytech.com/reference/v1-get-badges-org-meta` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| v1-update-badges-org-meta | none — live `docs.capillarytech.com/reference/v1-update-badges-org-meta` → 404 | `NO-SOURCE` | N/A — no source found (near-empty converted page: playground only) |
| _…154 more…_ | `INFO/backfill/raw/<slug>.md` | `OK` | (none — full prose preserved) |

<details>
<summary><strong>Full list of the 154 <code>OK</code> pages</strong> (source = <code>INFO/backfill/raw/&lt;slug&gt;.md</code>, all prose preserved)</summary>

`approve-or-reject-a-request`, `change-mfa-password`, `change-password`, `change_identifier-on-auth-engine`, `claim-badge`, `connectedorgs-enroll-customer-milestone-or-streak`, `connectedorgs-get-associated-target-groups-of-a-user`, `connectedorgs-get-customer-ledger-explode-info`, `connectedorgs-get-customer-ledger-info`, `connectedorgs-get-promotion-list-for-a-customer`, `connectedorgs-unenroll-customer-milestone-or-streak`, `create-a-loyalty-promotion`, `create-badges-group`, `create-badges-org`, `create-custom-field`, `create-data-field`, `create-fulfillment-status`, `create-group-reward`, `create-meta-search`, `create-points-restriction`, `create-promotion-for-ucc`, `create-reward-category`, `create-reward-expiry-reminder`, `create-rich-content-meta`, `createvendor`, `deactivate-search-criteria`, `delete-token`, `earn-promotion`, `enable-bulk-trigger`, `enable-custom-event-for-cortex-search`, `enable-disable-vendor`, `enable-or-disable-reward-categories`, `enrol-customers-for-badges`, `enrol-loyalty-promotion`, `forget-password`, `generate-authentication-tokenapi`, `generate-mfa-token`, `generate-otp-api`, `generate-otp-mfa`, `get-all-available-rewards-for-user-in-a-connected-org`, `get-all-badges`, `get-all-custom-fields`, `get-all-customer-badges`, `get-all-groups`, `get-all-requests`, `get-all-reward-transactions-for-a-user-in-connected-orgs`, `get-all-rich-text-content-metadata-for-brand`, `get-allocated-points-details-with-expiry-in-specified-date-range-global`, `get-available-brand-rewards`, `get-badge-by-id`, `get-badges-for-customer`, `get-brands-rewards`, `get-cart-promotion-redemptions`, `get-catalog-promotion-details`, `get-custom-field`, `get-custom-field-by-id`, `get-custom-fields-associated-with-coupon-redemption-global`, `get-customer-promotion-details`, `get-data-field-detail-api`, `get-entity-audit-logs`, `get-fixed-window-details`, `get-fulfillment-status`, `get-group-by-id`, `get-group-rewards`, `get-individual-badge-details-of-a-customer`, `get-list-of-catalog-promotions`, `get-list-of-vendor-redemption-details-by-vendor-id`, `get-lock-unlock-pending-carts`, `get-loyalty-promotion-all`, `get-loyalty-promotion-id`, `get-loyalty-promotion-list-for-a-program`, `get-meta`, `get-point-allocation-event-id-global`, `get-points-constraints`, `get-promotion-by-id`, `get-promotion-details`, `get-promotions-code-api`, `get-promotions-config-api`, `get-promotions-for-a-customer`, `get-promotions-for-a-particular-till`, `get-request-info`, `get-reward-category`, `get-reward-details-by-id`, `get-reward-details-by-id-in-a-connected-org`, `get-reward-expiry-reminder`, `get-reward-issue-transaction-details`, `get-reward-list`, `get-rewards-for-user-new-api`, `get-rewards-group-by-id`, `get-top-ranked-users`, `get-transaction-details-by-reward-transaction-id`, `get-transaction-details-by-reward-transaction-id-in-connected-orgs`, `get-user-brand-specific-rewards`, `get-user-ranks-across-target-groups`, `get-vendor-explode-info`, `get-vendor-list-for-specific-brand`, `get-vendor-redemption-details-by-brand-id-and-vendor-id`, `get-vendor-redemptions-details`, `import-earned-badges-of-customer`, `issue-badge-to-multiple-customers`, `issue-badge-to-the-customer`, `issue-bulk-reward`, `issue-bulk-reward-connected-org`, `issue-cart-promotion`, `list-member-promotions`, `list-member-promotions-explode`, `loyalty-promotions`, `mfa-forgot-password`, `organisation-level-configuration-for-rewards-catalog`, `post-cancel-cart-evaluation`, `post-create-catalog-promotion`, `post-create-custom-field`, `post-create-reward`, `post-earn-promotions-in-bulk`, `post-images-to-file-service`, `post-issue-user-reward`, `post-promotions-code-link-api`, `put-data-field-api`, `put-disable-catalog-promotion`, `put-update-catalog-promotion`, `put-update-reward`, `redeem-cart-promotion`, `regenerate-authentication-token`, `regenerate-token`, `retrieve-brand-id`, `retrieve-custom-event-config-details-for-entity`, `retrieve-custom-event-config-details-of-org`, `retrieve-organisation-level-configuration-for-rewards-catalog`, `review-loyalty-promotion`, `revoke-a-loyalty-promotion`, `revoke-enrolment-of-a-badge`, `revoke-issual-of-a-badge`, `search-api-cortex-api`, `submit-promotion-for-approval`, `unclaim-badge`, `update-badges`, `update-badges-group`, `update-custom-field`, `update-custom-field-badge`, `update-fulfillment-status`, `update-fulfilment-status-and-txn-custom-fields`, `update-group-reward`, `update-loyalty-promotion`, `update-points-restriction`, `update-reward-expiry-reminder`, `update-rich-content`, `update-the-activation-status-of-badge`, `update-vendor-redemption`, `upload-coupons-batch`, `upload-redeemed-coupons`, `validate-mfa-otp`, `validate-mfa-password`, `validate-otp-api`, `validate-password`

</details>

## 3. Missing content for every non-`OK` page

### 3a. `EMPTY-STUB` / `NEEDS-REVIEW` — local source exists, backfill from it

Each converted `.mdx` below currently contains only YAML frontmatter (≤1-line description). Restore the quoted source sections. Items marked *← prose, must backfill* are not rendered by the playground; unmarked items are playground-covered and listed only for completeness.

- **add-promotion-redemption** — `NEEDS-REVIEW` — source `INFO/backfill/raw/get-cart-promotion-redemptions.md` (source title: "Get Cart Promotion Redemptions"). Converted `.mdx` is frontmatter-only; restore:
    - `Example request`
    - `Prerequisites`  ← prose, must backfill
    - `Resource information`  ← prose, must backfill
    - `Query parameters`  ← prose, must backfill
    - `Example response`
    - `Response parameters`  ← prose, must backfill
    - `Error codes`  ← prose, must backfill
    - ⚠️ **Remap ambiguity:** `/api_gateway/v1/promotions/redemptions` (POST) is a shared path. The POST source is `INFO/backfill/raw/redeem-cart-promotion.md` ("Redeem Cart Promotion"); the GET-list source is `get-cart-promotion-redemptions.md` (shown above). Confirm which one "Add Promotion Redemption" should mirror before backfilling.

- **change-password-1** — `EMPTY-STUB` — source `INFO/backfill/raw/change-password.md` (source title: "Change Password"). Converted `.mdx` is frontmatter-only; restore:
    - `Prerequisite`  ← prose, must backfill
    - `Resource Information`  ← prose, must backfill
    - `Request URL`
    - `Example request`
    - `Request body parameters`
    - `Example response`

- **evaluate-promotion** — `EMPTY-STUB` — source `INFO/docs.capillarytech.com/API Reference/post_api-gateway-v1-promotions-evaluate.md` (source title: "Evaluate Promotions"). Converted `.mdx` is frontmatter-only; restore:
    - `Example request`
    - `Headers`
    - `Request Body`
    - `Example response`
    - `Response parameters`  ← prose, must backfill
    - `Error codes`  ← prose, must backfill
    - Callout/note: "Note"  ← prose, must backfill

- **forget-password-1** — `EMPTY-STUB` — source `INFO/backfill/raw/forget-password.md` (source title: "Forget Password"). Converted `.mdx` is frontmatter-only; restore:
    - `Resource Information`  ← prose, must backfill
    - `Request URL`
    - `Example request`
    - `Example response`

- **generate-authentication-token** — `EMPTY-STUB` — source `INFO/docs.capillarytech.com/API Reference/generateauthtoken.md` (source title: "Authentication Token"). Converted `.mdx` is frontmatter-only; restore:
    - `Request Body parameters`
    - `Response Body parameters`  ← prose, must backfill

- **generate-otp-1-1** — `EMPTY-STUB` — source `INFO/backfill/raw/generate-otp-api.md` (source title: "Generate OTP"). Converted `.mdx` is frontmatter-only; restore:
    - `Resource Information`  ← prose, must backfill
    - `Request URL`
    - `Request Body Parameters`
    - `Error`  ← prose, must backfill

- **get-custom-fields-associated-with-coupon-redemption** — `EMPTY-STUB` — source `INFO/backfill/raw/get-custom-fields-associated-with-coupon-redemption-global.md` (source title: "Get Custom Fields Associated with Coupon Redemption"). Converted `.mdx` is frontmatter-only; restore:
    - `Prerequisites`  ← prose, must backfill
    - `Resource information`  ← prose, must backfill
    - `API endpoint example`
    - `Request query parameters`
    - `Sample request`
    - `Response parameters`  ← prose, must backfill
    - `Sample response`
    - `Error Codes`  ← prose, must backfill
    - Callout/note: "Note"  ← prose, must backfill

- **get-customer-reward-transactions** — `EMPTY-STUB` — source `capillary-migrator/verseion-2-capillary-api-references/get-all-reward-transactions-for-a-user.md` (source title: "Get customer reward transactions"). Converted `.mdx` is frontmatter-only; restore:
    - `Example request`
    - `Prerequisites`  ← prose, must backfill
    - `Resource information`  ← prose, must backfill
    - `API endpoint example`
    - `Request path parameters`
    - `Request query parameters`
    - `Response parameters`  ← prose, must backfill
    - Callout/note: "Note"  ← prose, must backfill

- **issue-user-reward** — `EMPTY-STUB` — source `INFO/backfill/raw/post-issue-user-reward.md` (source title: "Issue single reward"). Converted `.mdx` is frontmatter-only; restore:
    - `Prerequisites`  ← prose, must backfill
    - `Resource information`  ← prose, must backfill
    - `API endpoint example`
    - `Request query parameters`
    - `Request body parameters`
    - `Response parameters`  ← prose, must backfill
    - `Example: Issuing reward with quantity and redemption value`  ← prose, must backfill
    - `Example: Issuing reward with only quantity`  ← prose, must backfill
    - `Response parameters`  ← prose, must backfill
    - `Example: Issuing reward with quantity and redemption value`  ← prose, must backfill
    - `Example: Issuing reward with only quantity`  ← prose, must backfill
    - `Example: Issuing reward created for customer segment`  ← prose, must backfill
    - `API-specific error codes`  ← prose, must backfill
    - Callout/note: "️ Issuing a reward created for a customer segment"  ← prose, must backfill
    - Callout/note: "Note"  ← prose, must backfill

- **post-revoke-earned-promotion** — `EMPTY-STUB` — source `INFO/docs.capillarytech.com/API Reference/revoke-earned-promotion.md` (source title: "Revoke Earned Cart Promotion"). Converted `.mdx` is frontmatter-only; restore:
    - `Example request`
    - `Prerequisites`  ← prose, must backfill
    - `Resource information`  ← prose, must backfill
    - `Request body parameters`
    - `Example response`
    - `Response parameters`  ← prose, must backfill
    - `Error codes`  ← prose, must backfill

- **regenerate-authentication-token-1** — `EMPTY-STUB` — source `INFO/backfill/raw/regenerate-authentication-token.md` (source title: "Regenerate Authentication Token"). Converted `.mdx` is frontmatter-only; restore:
    - `Resource Information`  ← prose, must backfill
    - `Request URL`
    - `Example request`
    - `Example response`
    - `Error code`  ← prose, must backfill

- **validate-otp-2** — `EMPTY-STUB` — source `INFO/backfill/raw/validate-otp-api.md` (source title: "Validate OTP"). Converted `.mdx` is frontmatter-only; restore:
    - `Resource Information`  ← prose, must backfill
    - `Request URL`
    - `Request Body Parameters`
    - `Sample response`
    - `Response parameters`  ← prose, must backfill

- **validate-password-1** — `EMPTY-STUB` — source `INFO/backfill/raw/validate-password.md` (source title: "Validate Password"). Converted `.mdx` is frontmatter-only; restore:
    - `Resource Information`  ← prose, must backfill
    - `Request URL`
    - `Request Body Parameters`
    - `Response parameters`  ← prose, must backfill
    - `Error code`  ← prose, must backfill

### 3b. `NO-SOURCE` — no source located; near-empty converted pages

These 29 converted pages render as API-playground-only. No ReadMe source was found locally or at the destination slug on the live site. They are flagged so the team can locate the original ReadMe slug (if any) and backfill; per the audit rules the original slug was **not** guessed.

- **add-promotion** — live `docs.capillarytech.com/reference/add-promotion` → 404. No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **claim-reward** — live `docs.capillarytech.com/reference/claim-reward` → 404. No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **create-reward-currency-limits** — live `docs.capillarytech.com/reference/create-reward-currency-limits` → 404. No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-allocated-points-details-with-event-id** — live `docs.capillarytech.com/reference/get-allocated-points-details-with-event-id` → 404. No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-brand-rewards** — live `docs.capillarytech.com/reference/get-brand-rewards` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-cart-recommendations** — live `docs.capillarytech.com/reference/get-cart-recommendations` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-custom-field-for-transaction-add-event** — live `docs.capillarytech.com/reference/get-custom-field-for-transaction-add-event` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-event-log-ids-for-credit-debit** — live `docs.capillarytech.com/reference/get-event-log-ids-for-credit-debit` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-event-log-ids-with-credit-or-debit-for-alternate-currency** — live `docs.capillarytech.com/reference/get-event-log-ids-with-credit-or-debit-for-alternate-currency` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-expired-points-for-a-customer** — live `docs.capillarytech.com/reference/get-expired-points-for-a-customer` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-item-recommendations** — live `docs.capillarytech.com/reference/get-item-recommendations` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-ledger-information-for-event-log-ids** — live `docs.capillarytech.com/reference/get-ledger-information-for-event-log-ids` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-manual-points-adjustment-details** — live `docs.capillarytech.com/reference/get-manual-points-adjustment-details` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-point-redemption-details-for-redemption-events** — live `docs.capillarytech.com/reference/get-point-redemption-details-for-redemption-events` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-reward-currency-limits** — live `docs.capillarytech.com/reference/get-reward-currency-limits` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-target-completion-details** — live `docs.capillarytech.com/reference/get-target-completion-details` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-user-recommendations** — live `docs.capillarytech.com/reference/get-user-recommendations` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-user-reward** — live `docs.capillarytech.com/reference/get-user-reward` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-user-rewards** — live `docs.capillarytech.com/reference/get-user-rewards` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-user-rewards-merge-details** — live `docs.capillarytech.com/reference/get-user-rewards-merge-details` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **get-user-specific-reward-by-id** — live `docs.capillarytech.com/reference/get-user-specific-reward-by-id` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **merge-user-rewards** — live `docs.capillarytech.com/reference/merge-user-rewards` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **post_loyalty-api-v1-workflows-expjson-programid-eventtype** — live `docs.capillarytech.com/reference/post_loyalty-api-v1-workflows-expjson-programid-eventtype` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **retrieve-pa-id-for-specified-data-range** — live `docs.capillarytech.com/reference/retrieve-pa-id-for-specified-data-range` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **retrieve-pointsaward-records-associated-with-a-specific-transaction-id** — live `docs.capillarytech.com/reference/retrieve-pointsaward-records-associated-with-a-specific-transaction-id` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **set-promotion-settings** — live `docs.capillarytech.com/reference/set-promotion-settings` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **v1-create-badges-org-meta** — live `docs.capillarytech.com/reference/v1-create-badges-org-meta` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **v1-get-badges-org-meta** — live `docs.capillarytech.com/reference/v1-get-badges-org-meta` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.
- **v1-update-badges-org-meta** — live `docs.capillarytech.com/reference/v1-update-badges-org-meta` → 404 (destination slug not found on ReadMe). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only (+ ≤1-line description) and renders as **API-playground-only, no prose**. If this endpoint had a ReadMe page under a different slug, its prose is unrecovered; original slug unknown, not guessed.


---
_Read-only audit. No `.mdx`, `.yaml`, or `documentation.json` file was modified._
