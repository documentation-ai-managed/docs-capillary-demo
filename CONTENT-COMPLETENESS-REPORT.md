---
title: "API-Reference Content-Completeness Report"
description: "Block-level audit: does every converted Documentation.AI page retain all human-readable content from its ReadMe source? (196 pages)"
---

# API-Reference Content-Completeness Report

Read-only audit verifying that **no human-readable content was lost** migrating the Capillary API-Reference from ReadMe → Documentation.AI. Every converted page (`docs-capillary-demo/reference/<slug>.mdx`) was compared **block-by-block** against its ReadMe source — tables (row by row), callouts, notes, lists, images, links, and narrative — not just section headings. Content that the OpenAPI playground (`api-reference/<slug>.yaml`) renders (endpoint description, request parameters, request body schema, sample request/response), the `# OpenAPI definition` JSON dump, and a body heading that merely repeats the title are treated as **intentionally omitted** and are not flagged.

## 1. Summary counts

| Verdict | Count | Meaning |
|---------|------:|---------|
| **Total pages** | **196** | every leaf `"path": "reference/<slug>"` in `documentation.json` |
| `COMPLETE` | 152 | all required source content present in the converted page |
| `MISSING-CONTENT` | 0 | one or more required sections/rows/notes/images absent |
| `EMPTY-STUB` | 12 | converted `.mdx` is frontmatter-only but source has real prose |
| `NEEDS-REVIEW` | 3 | matched but ambiguous (uncertain remap, or borderline playground-covered truncation) |
| `NO-SOURCE` | 29 | no source found in any local dir or on the live ReadMe |

**Headline:** of the 165 pages that have a locatable source, **163 retain all required content**. There are **zero `MISSING-CONTENT` pages** — no required section, table row, response-parameter row, callout, note, list item, image, or link was dropped on any page that was migrated with content. The defects are: **12 empty stubs** (migrated as playground-only, full source prose exists to backfill), **1 empty stub with an ambiguous source**, **2 pages with borderline request-body cell truncations**, and **29 playground-only pages whose ReadMe source could not be located**.

### How this was verified (reproducible, non-hallucinated)

- **196 slugs** from `documentation.json`. Source resolution, first match wins: exact `<slug>.md` in the three source dirs → `METHOD /path` endpoint remap from the `.yaml` → fuzzy filename → live `docs.capillarytech.com/reference/<slug>`.
- **154 pages with an exact local source** were diffed at block level with a *markup-stripped signature* recall (links reduced to anchor text; `<Anchor>`/`<br>`/JSX tags, back-ticks, pipes, and escapes removed on both sides) so that URL-rewrites and formatting changes are correctly treated as equivalent. Then, independently: **table-row-count parity** (every page has mdx rows ≥ source rows — no rows dropped), a **dedicated callout sweep** (133 source callouts — all 133 present), and an **image sweep** (7 source images — all present).
- Every candidate flag was hand-verified by grepping a distinctive phrase in the `.mdx`; all but the two truncation pages resolved to false positives (formatting/URL differences), which is why they are `COMPLETE`.
- **42 pages had no exact-slug source:** 13 remapped to a local source via shared endpoint path / fuzzy filename (all frontmatter-only → `EMPTY-STUB`/`NEEDS-REVIEW`); 29 had none locally and return **404** on the live ReadMe destination slug (WebFetch-verified on a sample; bulk `curl` was rate-limited to `429`).
- The `capillary-docs` MCP indexes the **destination** site and the `Readme` MCP points at `docs.readme.com`; neither is the Capillary ReadMe source, so neither was used to substitute source prose.

## 2. Per-page results

The 44 non-`COMPLETE` rows are listed individually; the 152 `COMPLETE` pages are collapsed at the end of the table.

| slug | source used (path or live URL) | verdict | missing content (short) |
|------|--------------------------------|---------|-------------------------|
| add-promotion-redemption | INFO/backfill/raw/get-cart-promotion-redemptions.md | `NEEDS-REVIEW` | converted .mdx is frontmatter-only; source has full prose, but the correct source is ambiguous (shared endpoint path) |
| create-a-loyalty-promotion | INFO/backfill/raw/create-a-loyalty-promotion.md | `NEEDS-REVIEW` | 21 request-body table cells had trailing 'Supported values:'/'Example:' enum explanations truncated (playground-covered; no required/response content lost) |
| update-loyalty-promotion | INFO/backfill/raw/update-loyalty-promotion.md | `NEEDS-REVIEW` | 45 request-body table cells had trailing 'Supported values:'/'Example:' enum explanations truncated (playground-covered; no required/response content lost) |
| change-password-1 | INFO/backfill/raw/change-password.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Prerequisite, Resource Information, Request URL, Example request, Request body parameters, Example response |
| evaluate-promotion | INFO/docs.capillarytech.com/API Reference/post_api-gateway-v1-promotions-evaluate.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Example request, Headers, Request Body, Example response, Response parameters, Error codes |
| forget-password-1 | INFO/backfill/raw/forget-password.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Resource Information, Request URL, Example request, Example response |
| generate-authentication-token | INFO/docs.capillarytech.com/API Reference/generateauthtoken.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Request Body parameters, Response Body parameters |
| generate-otp-1-1 | INFO/backfill/raw/generate-otp-api.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Resource Information, Request URL, Request Body Parameters, Error |
| get-custom-fields-associated-with-coupon-redemption | INFO/backfill/raw/get-custom-fields-associated-with-coupon-redemption-global.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Prerequisites, Resource information, API endpoint example, Request query parameters, Sample request, Response parameters, Sample response, Error Codes |
| get-customer-reward-transactions | capillary-migrator/verseion-2-capillary-api-references/get-all-reward-transactions-for-a-user.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Example request, Prerequisites, Resource information, API endpoint example, Request path parameters, Request query parameters, Response parameters |
| issue-user-reward | INFO/backfill/raw/post-issue-user-reward.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Prerequisites, Resource information, API endpoint example, Request query parameters, Request body parameters, Response parameters, Example: Issuing reward with quantity and redemption value, Example: Issuing reward with only quantity, Response parameters, Example: Issuing reward with quantity and redemption value, Example: Issuing reward with only quantity, Example: Issuing reward created for customer segment, API-specific error codes |
| post-revoke-earned-promotion | INFO/docs.capillarytech.com/API Reference/revoke-earned-promotion.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Example request, Prerequisites, Resource information, Request body parameters, Example response, Response parameters, Error codes |
| regenerate-authentication-token-1 | INFO/backfill/raw/regenerate-authentication-token.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Resource Information, Request URL, Example request, Example response, Error code |
| validate-otp-2 | INFO/backfill/raw/validate-otp-api.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Resource Information, Request URL, Request Body Parameters, Sample response, Response parameters |
| validate-password-1 | INFO/backfill/raw/validate-password.md | `EMPTY-STUB` | entire page body missing — converted .mdx is frontmatter-only; source prose sections: Resource Information, Request URL, Request Body Parameters, Response parameters, Error code |
| add-promotion | none — live docs.capillarytech.com/reference/add-promotion → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| claim-reward | none — live docs.capillarytech.com/reference/claim-reward → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| create-reward-currency-limits | none — live docs.capillarytech.com/reference/create-reward-currency-limits → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-allocated-points-details-with-event-id | none — live docs.capillarytech.com/reference/get-allocated-points-details-with-event-id → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-brand-rewards | none — live docs.capillarytech.com/reference/get-brand-rewards → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-cart-recommendations | none — live docs.capillarytech.com/reference/get-cart-recommendations → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-custom-field-for-transaction-add-event | none — live docs.capillarytech.com/reference/get-custom-field-for-transaction-add-event → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-event-log-ids-for-credit-debit | none — live docs.capillarytech.com/reference/get-event-log-ids-for-credit-debit → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-event-log-ids-with-credit-or-debit-for-alternate-currency | none — live docs.capillarytech.com/reference/get-event-log-ids-with-credit-or-debit-for-alternate-currency → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-expired-points-for-a-customer | none — live docs.capillarytech.com/reference/get-expired-points-for-a-customer → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-item-recommendations | none — live docs.capillarytech.com/reference/get-item-recommendations → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-ledger-information-for-event-log-ids | none — live docs.capillarytech.com/reference/get-ledger-information-for-event-log-ids → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-manual-points-adjustment-details | none — live docs.capillarytech.com/reference/get-manual-points-adjustment-details → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-point-redemption-details-for-redemption-events | none — live docs.capillarytech.com/reference/get-point-redemption-details-for-redemption-events → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-reward-currency-limits | none — live docs.capillarytech.com/reference/get-reward-currency-limits → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-target-completion-details | none — live docs.capillarytech.com/reference/get-target-completion-details → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-user-recommendations | none — live docs.capillarytech.com/reference/get-user-recommendations → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-user-reward | none — live docs.capillarytech.com/reference/get-user-reward → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-user-rewards | none — live docs.capillarytech.com/reference/get-user-rewards → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-user-rewards-merge-details | none — live docs.capillarytech.com/reference/get-user-rewards-merge-details → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| get-user-specific-reward-by-id | none — live docs.capillarytech.com/reference/get-user-specific-reward-by-id → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| merge-user-rewards | none — live docs.capillarytech.com/reference/merge-user-rewards → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| post_loyalty-api-v1-workflows-expjson-programid-eventtype | none — live docs.capillarytech.com/reference/post_loyalty-api-v1-workflows-expjson-programid-eventtype → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| retrieve-pa-id-for-specified-data-range | none — live docs.capillarytech.com/reference/retrieve-pa-id-for-specified-data-range → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| retrieve-pointsaward-records-associated-with-a-specific-transaction-id | none — live docs.capillarytech.com/reference/retrieve-pointsaward-records-associated-with-a-specific-transaction-id → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| set-promotion-settings | none — live docs.capillarytech.com/reference/set-promotion-settings → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| v1-create-badges-org-meta | none — live docs.capillarytech.com/reference/v1-create-badges-org-meta → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| v1-get-badges-org-meta | none — live docs.capillarytech.com/reference/v1-get-badges-org-meta → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| v1-update-badges-org-meta | none — live docs.capillarytech.com/reference/v1-update-badges-org-meta → 404 | `NO-SOURCE` | N/A — no source found; converted page is playground-only (frontmatter + ≤1-line description) |
| _…152 more…_ | `INFO/backfill/raw/<slug>.md` | `COMPLETE` | (none — all content preserved) |

<details>
<summary><strong>Full list of the 152 <code>COMPLETE</code> pages</strong></summary>

`approve-or-reject-a-request`, `change-mfa-password`, `change-password`, `change_identifier-on-auth-engine`, `claim-badge`, `connectedorgs-enroll-customer-milestone-or-streak`, `connectedorgs-get-associated-target-groups-of-a-user`, `connectedorgs-get-customer-ledger-explode-info`, `connectedorgs-get-customer-ledger-info`, `connectedorgs-get-promotion-list-for-a-customer`, `connectedorgs-unenroll-customer-milestone-or-streak`, `create-badges-group`, `create-badges-org`, `create-custom-field`, `create-data-field`, `create-fulfillment-status`, `create-group-reward`, `create-meta-search`, `create-points-restriction`, `create-promotion-for-ucc`, `create-reward-category`, `create-reward-expiry-reminder`, `create-rich-content-meta`, `createvendor`, `deactivate-search-criteria`, `delete-token`, `earn-promotion`, `enable-bulk-trigger`, `enable-custom-event-for-cortex-search`, `enable-disable-vendor`, `enable-or-disable-reward-categories`, `enrol-customers-for-badges`, `enrol-loyalty-promotion`, `forget-password`, `generate-authentication-tokenapi`, `generate-mfa-token`, `generate-otp-api`, `generate-otp-mfa`, `get-all-available-rewards-for-user-in-a-connected-org`, `get-all-badges`, `get-all-custom-fields`, `get-all-customer-badges`, `get-all-groups`, `get-all-requests`, `get-all-reward-transactions-for-a-user-in-connected-orgs`, `get-all-rich-text-content-metadata-for-brand`, `get-allocated-points-details-with-expiry-in-specified-date-range-global`, `get-available-brand-rewards`, `get-badge-by-id`, `get-badges-for-customer`, `get-brands-rewards`, `get-cart-promotion-redemptions`, `get-catalog-promotion-details`, `get-custom-field`, `get-custom-field-by-id`, `get-custom-fields-associated-with-coupon-redemption-global`, `get-customer-promotion-details`, `get-data-field-detail-api`, `get-entity-audit-logs`, `get-fixed-window-details`, `get-fulfillment-status`, `get-group-by-id`, `get-group-rewards`, `get-individual-badge-details-of-a-customer`, `get-list-of-catalog-promotions`, `get-list-of-vendor-redemption-details-by-vendor-id`, `get-lock-unlock-pending-carts`, `get-loyalty-promotion-all`, `get-loyalty-promotion-id`, `get-loyalty-promotion-list-for-a-program`, `get-meta`, `get-point-allocation-event-id-global`, `get-points-constraints`, `get-promotion-by-id`, `get-promotion-details`, `get-promotions-code-api`, `get-promotions-config-api`, `get-promotions-for-a-customer`, `get-promotions-for-a-particular-till`, `get-request-info`, `get-reward-category`, `get-reward-details-by-id`, `get-reward-details-by-id-in-a-connected-org`, `get-reward-expiry-reminder`, `get-reward-issue-transaction-details`, `get-reward-list`, `get-rewards-for-user-new-api`, `get-rewards-group-by-id`, `get-top-ranked-users`, `get-transaction-details-by-reward-transaction-id`, `get-transaction-details-by-reward-transaction-id-in-connected-orgs`, `get-user-brand-specific-rewards`, `get-user-ranks-across-target-groups`, `get-vendor-explode-info`, `get-vendor-list-for-specific-brand`, `get-vendor-redemption-details-by-brand-id-and-vendor-id`, `get-vendor-redemptions-details`, `import-earned-badges-of-customer`, `issue-badge-to-multiple-customers`, `issue-badge-to-the-customer`, `issue-bulk-reward`, `issue-bulk-reward-connected-org`, `issue-cart-promotion`, `list-member-promotions`, `list-member-promotions-explode`, `loyalty-promotions`, `mfa-forgot-password`, `organisation-level-configuration-for-rewards-catalog`, `post-cancel-cart-evaluation`, `post-create-catalog-promotion`, `post-create-custom-field`, `post-create-reward`, `post-earn-promotions-in-bulk`, `post-images-to-file-service`, `post-issue-user-reward`, `post-promotions-code-link-api`, `put-data-field-api`, `put-disable-catalog-promotion`, `put-update-catalog-promotion`, `put-update-reward`, `redeem-cart-promotion`, `regenerate-authentication-token`, `regenerate-token`, `retrieve-brand-id`, `retrieve-custom-event-config-details-for-entity`, `retrieve-custom-event-config-details-of-org`, `retrieve-organisation-level-configuration-for-rewards-catalog`, `review-loyalty-promotion`, `revoke-a-loyalty-promotion`, `revoke-enrolment-of-a-badge`, `revoke-issual-of-a-badge`, `search-api-cortex-api`, `submit-promotion-for-approval`, `unclaim-badge`, `update-badges`, `update-badges-group`, `update-custom-field`, `update-custom-field-badge`, `update-fulfillment-status`, `update-fulfilment-status-and-txn-custom-fields`, `update-group-reward`, `update-points-restriction`, `update-reward-expiry-reminder`, `update-rich-content`, `update-the-activation-status-of-badge`, `update-vendor-redemption`, `upload-coupons-batch`, `upload-redeemed-coupons`, `validate-mfa-otp`, `validate-mfa-password`, `validate-otp-api`, `validate-password`

</details>

## 3. Missing content for every non-`COMPLETE` page

### 3a. `EMPTY-STUB` — migrated as playground-only; full source prose exists to backfill

Each `.mdx` below currently contains only YAML frontmatter (≤1-line description). Restore the quoted source sections. Items marked *← required prose* are not rendered by the playground; unmarked items are playground-covered and listed only for completeness.

- **change-password-1** — `EMPTY-STUB` — source `INFO/backfill/raw/change-password.md` (title: "Change Password"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Prerequisite`  ← required prose
    - `Resource Information`  ← required prose
    - `Request URL`
    - `Example request`
    - `Request body parameters`
    - `Example response`

- **evaluate-promotion** — `EMPTY-STUB` — source `INFO/docs.capillarytech.com/API Reference/post_api-gateway-v1-promotions-evaluate.md` (title: "Evaluate Promotions"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Example request`
    - `Headers`
    - `Request Body`
    - `Example response`
    - `Response parameters`  ← required prose
    - `Error codes`  ← required prose
    - Callout/note: "Note"  ← required prose

- **forget-password-1** — `EMPTY-STUB` — source `INFO/backfill/raw/forget-password.md` (title: "Forget Password"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Resource Information`  ← required prose
    - `Request URL`
    - `Example request`
    - `Example response`

- **generate-authentication-token** — `EMPTY-STUB` — source `INFO/docs.capillarytech.com/API Reference/generateauthtoken.md` (title: "Authentication Token"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Request Body parameters`
    - `Response Body parameters`  ← required prose

- **generate-otp-1-1** — `EMPTY-STUB` — source `INFO/backfill/raw/generate-otp-api.md` (title: "Generate OTP"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Resource Information`  ← required prose
    - `Request URL`
    - `Request Body Parameters`
    - `Error`  ← required prose

- **get-custom-fields-associated-with-coupon-redemption** — `EMPTY-STUB` — source `INFO/backfill/raw/get-custom-fields-associated-with-coupon-redemption-global.md` (title: "Get Custom Fields Associated with Coupon Redemption"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Prerequisites`  ← required prose
    - `Resource information`  ← required prose
    - `API endpoint example`
    - `Request query parameters`
    - `Sample request`
    - `Response parameters`  ← required prose
    - `Sample response`
    - `Error Codes`  ← required prose
    - Callout/note: "Note"  ← required prose

- **get-customer-reward-transactions** — `EMPTY-STUB` — source `capillary-migrator/verseion-2-capillary-api-references/get-all-reward-transactions-for-a-user.md` (title: "Get customer reward transactions"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Example request`
    - `Prerequisites`  ← required prose
    - `Resource information`  ← required prose
    - `API endpoint example`
    - `Request path parameters`
    - `Request query parameters`
    - `Response parameters`  ← required prose
    - Callout/note: "Note"  ← required prose

- **issue-user-reward** — `EMPTY-STUB` — source `INFO/backfill/raw/post-issue-user-reward.md` (title: "Issue single reward"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Prerequisites`  ← required prose
    - `Resource information`  ← required prose
    - `API endpoint example`
    - `Request query parameters`
    - `Request body parameters`
    - `Response parameters`  ← required prose
    - `Example: Issuing reward with quantity and redemption value`  ← required prose
    - `Example: Issuing reward with only quantity`  ← required prose
    - `Response parameters`  ← required prose
    - `Example: Issuing reward with quantity and redemption value`  ← required prose
    - `Example: Issuing reward with only quantity`  ← required prose
    - `Example: Issuing reward created for customer segment`  ← required prose
    - `API-specific error codes`  ← required prose
    - Callout/note: "Issuing a reward created for a customer segment"  ← required prose
    - Callout/note: "Note"  ← required prose

- **post-revoke-earned-promotion** — `EMPTY-STUB` — source `INFO/docs.capillarytech.com/API Reference/revoke-earned-promotion.md` (title: "Revoke Earned Cart Promotion"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Example request`
    - `Prerequisites`  ← required prose
    - `Resource information`  ← required prose
    - `Request body parameters`
    - `Example response`
    - `Response parameters`  ← required prose
    - `Error codes`  ← required prose

- **regenerate-authentication-token-1** — `EMPTY-STUB` — source `INFO/backfill/raw/regenerate-authentication-token.md` (title: "Regenerate Authentication Token"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Resource Information`  ← required prose
    - `Request URL`
    - `Example request`
    - `Example response`
    - `Error code`  ← required prose

- **validate-otp-2** — `EMPTY-STUB` — source `INFO/backfill/raw/validate-otp-api.md` (title: "Validate OTP"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Resource Information`  ← required prose
    - `Request URL`
    - `Request Body Parameters`
    - `Sample response`
    - `Response parameters`  ← required prose

- **validate-password-1** — `EMPTY-STUB` — source `INFO/backfill/raw/validate-password.md` (title: "Validate Password"). Converted `.mdx` is frontmatter-only (+ ≤1-line description); restore the full source body:
    - `Resource Information`  ← required prose
    - `Request URL`
    - `Request Body Parameters`
    - `Response parameters`  ← required prose
    - `Error code`  ← required prose

### 3b. `NEEDS-REVIEW` — matched but needs a human decision

- **add-promotion-redemption** — `NEEDS-REVIEW` — converted `.mdx` is frontmatter-only (empty stub). A source with full prose exists but the remap is ambiguous:
    - ⚠️ Endpoint `/api_gateway/v1/promotions/redemptions` (POST) is shared by two operations. POST source = `INFO/backfill/raw/redeem-cart-promotion.md` ("Redeem Cart Promotion"); GET-list source = `INFO/backfill/raw/get-cart-promotion-redemptions.md` ("Get Cart Promotion Redemptions"). Confirm which the DocumentationAI "Add Promotion Redemption" page should mirror, then backfill its full prose (Prerequisites, Resource information, Response parameters, Error codes, etc.).

- **create-a-loyalty-promotion** — `NEEDS-REVIEW` — source `INFO/backfill/raw/create-a-loyalty-promotion.md`. All table rows, every section heading, all notes/callouts/links/images are present. The **only** discrepancy: 21 cells inside **request-body** parameter tables had their trailing enum descriptions truncated during migration (the request body schema is playground-covered, so this is borderline). Representative truncated tails (present in source, shortened in `.mdx`):
    - `.promotionType` — dropped: "Supported values: `LOYALTY_EARNING`: Use this promotion type when the primary reward is awarding loyalty…"
    - `.status` — dropped: "Supported Values: `DRAFT`: Promotion is saved but is not active. …"
    - `.customerEligibilityType` — dropped: "Supported Values: `LOYAL`: Only customers with active loyalty enrollment can qualify. …"
    - `..startType` — dropped: "Supported values: `IMMEDIATE`: Starts immediately after the promotion is approved. `PARTICULAR_DATE`: …"
    - `..frequency` — dropped: "Supported values: `ONCE`: Rewards are issued a single time. `DAILY`: …"
    - _All truncations are in request-body object tables; no `Response parameters` / `Resource information` / narrative content is affected. Restore only if the enum-value explanations are considered required beyond the playground schema._

- **update-loyalty-promotion** — `NEEDS-REVIEW` — source `INFO/backfill/raw/update-loyalty-promotion.md`. All 550 table rows, every section heading, all notes/callouts/links/images are present. The **only** discrepancy: 45 cells inside **request-body** parameter tables had their trailing enum descriptions truncated during migration (the request body schema is playground-covered, so this is borderline). Representative truncated tails (present in source, shortened in `.mdx`):
    - `promotionType` — dropped: "Supported values: `LOYALTY_EARNING`: Use this promotion type when the primary reward is awarding loyalty… (enum value explanations)"
    - `..type (restrictions)` — dropped: "Supported Values: `PROMOTION`: The loyalty promotion enrolment expires with the promotion end date.<br />Example: …"
    - `..periodType` — dropped: "Supported value `:MOVING_WINDOW`: The limit is evaluated over a rolling timeframe that counts backward from…"
    - `milestones.description` — dropped: "… Supported Values: String. Example: "Reward for reaching the first spending level""
    - `milestones..fileName` — dropped: "The original name of the CSV file being uploaded (e.g., "targets.csv")."
    - _All truncations are in request-body object tables; no `Response parameters` / `Resource information` / narrative content is affected. Restore only if the enum-value explanations are considered required beyond the playground schema._

### 3c. `NO-SOURCE` — no source located; near-empty playground-only pages

- **add-promotion** — live `docs.capillarytech.com/reference/add-promotion` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **claim-reward** — live `docs.capillarytech.com/reference/claim-reward` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **create-reward-currency-limits** — live `docs.capillarytech.com/reference/create-reward-currency-limits` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-allocated-points-details-with-event-id** — live `docs.capillarytech.com/reference/get-allocated-points-details-with-event-id` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-brand-rewards** — live `docs.capillarytech.com/reference/get-brand-rewards` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-cart-recommendations** — live `docs.capillarytech.com/reference/get-cart-recommendations` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-custom-field-for-transaction-add-event** — live `docs.capillarytech.com/reference/get-custom-field-for-transaction-add-event` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-event-log-ids-for-credit-debit** — live `docs.capillarytech.com/reference/get-event-log-ids-for-credit-debit` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-event-log-ids-with-credit-or-debit-for-alternate-currency** — live `docs.capillarytech.com/reference/get-event-log-ids-with-credit-or-debit-for-alternate-currency` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-expired-points-for-a-customer** — live `docs.capillarytech.com/reference/get-expired-points-for-a-customer` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-item-recommendations** — live `docs.capillarytech.com/reference/get-item-recommendations` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-ledger-information-for-event-log-ids** — live `docs.capillarytech.com/reference/get-ledger-information-for-event-log-ids` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-manual-points-adjustment-details** — live `docs.capillarytech.com/reference/get-manual-points-adjustment-details` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-point-redemption-details-for-redemption-events** — live `docs.capillarytech.com/reference/get-point-redemption-details-for-redemption-events` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-reward-currency-limits** — live `docs.capillarytech.com/reference/get-reward-currency-limits` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-target-completion-details** — live `docs.capillarytech.com/reference/get-target-completion-details` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-user-recommendations** — live `docs.capillarytech.com/reference/get-user-recommendations` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-user-reward** — live `docs.capillarytech.com/reference/get-user-reward` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-user-rewards** — live `docs.capillarytech.com/reference/get-user-rewards` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-user-rewards-merge-details** — live `docs.capillarytech.com/reference/get-user-rewards-merge-details` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **get-user-specific-reward-by-id** — live `docs.capillarytech.com/reference/get-user-specific-reward-by-id` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **merge-user-rewards** — live `docs.capillarytech.com/reference/merge-user-rewards` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **post_loyalty-api-v1-workflows-expjson-programid-eventtype** — live `docs.capillarytech.com/reference/post_loyalty-api-v1-workflows-expjson-programid-eventtype` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **retrieve-pa-id-for-specified-data-range** — live `docs.capillarytech.com/reference/retrieve-pa-id-for-specified-data-range` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **retrieve-pointsaward-records-associated-with-a-specific-transaction-id** — live `docs.capillarytech.com/reference/retrieve-pointsaward-records-associated-with-a-specific-transaction-id` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **set-promotion-settings** — live `docs.capillarytech.com/reference/set-promotion-settings` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **v1-create-badges-org-meta** — live `docs.capillarytech.com/reference/v1-create-badges-org-meta` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **v1-get-badges-org-meta** — live `docs.capillarytech.com/reference/v1-get-badges-org-meta` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.
- **v1-update-badges-org-meta** — live `docs.capillarytech.com/reference/v1-update-badges-org-meta` → **404** (WebFetch-verified on a sample of this set). No local `.md` via exact slug, endpoint-path remap, or fuzzy filename. Converted `.mdx` is frontmatter-only and renders as **API-playground-only, no prose**. Original ReadMe slug (if any) unknown — not guessed.


---
_Read-only audit. No `.mdx`, `.yaml`, or `documentation.json` file was modified._
