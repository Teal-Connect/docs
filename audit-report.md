# Docs Audit Report — Issue #103

**Date:** 2026-06-23
**Method:** LLM agent audit of the full doc set, with `api-reference/openapi.yaml` as ground truth.
**Scope:** Content `.mdx` pages + `docs.json` navigation + the hand-maintained `api-reference/openapi.yaml`. Auto-generated endpoint `.mdx` pages under `api-reference/**` and the `generator/python/` directory are treated as ground truth — flagged only, not hand-edited.

## Doc set inventory (baseline)

**Content pages (in nav):**
- `introduction.mdx` — product/ecosystem overview
- `sandbox.mdx` — sandbox walkthrough (757 lines, the main guide)
- `data.mdx` — data model / endpoints reference
- `authorisations.mdx` — authorisation terms + records
- `recurring-checks.mdx` — recurring payroll (nav title "Recurring Payroll")

**API Reference (in nav):** `api-reference/quickstart.mdx`, `api-reference/webhooks` + ~50 generated endpoint `.mdx` pages.

**Orphan files (NOT in nav):** `essentials/code.mdx`, `essentials/images.mdx`, `essentials/markdown.mdx`, `essentials/navigation.mdx`, `essentials/reusable-snippets.mdx`, `essentials/settings.mdx` — all unmodified Mintlify Starter Kit template pages referencing `mint.json`.

**Ground truth:** `api-reference/openapi.yaml` (title "Payroll API", version 1.0.0, servers `https://api.sandbox.goteal.co` and `https://api.goteal.co` — no `/v1` prefix).

---

## (a) Duplicate / overlapping information

| # | Location | Issue | Action |
|---|----------|-------|--------|
| D1 | `data.mdx` §1 Endpoints vs `sandbox.mdx` | Endpoint descriptions + curl examples overlap heavily. `data.mdx` is the conceptual reference; `sandbox.mdx` is the step-by-step walkthrough. Acceptable split, but no cross-links. | Add cross-links; keep `data.mdx` as source-of-truth for the data model. |
| D2 | Webhook setup curl appears in `introduction.mdx`, `sandbox.mdx`, `data.mdx` | Three near-identical webhook-create snippets, with **divergent field names** (see O3). | Reconcile field names; point conceptual pages at sandbox/API-ref. |

## (b) Outdated content vs current API (`openapi.yaml`)

| # | Location | Issue | Fix |
|---|----------|-------|-----|
| O1 | `api-reference/quickstart.mdx:51-52` | Environments base URLs given as `https://api.goteal.co/v1` and `https://api.sandbox.goteal.co/v1`. openapi servers have **no `/v1`** and all endpoint paths are unprefixed (`/users`, `/accounts`, …). Every curl example in the docs uses the no-`/v1` form. | Remove `/v1` to match openapi + examples. |
| O2 | `data.mdx:166-167` | Webhook `events` includes `user-payroll-created` — not a valid event. openapi enum: `user-payroll-submitted`, `user-hmrc-data-submitted`, `user-bank-statement-submitted`. | Replace with valid event(s). |
| O3 | `data.mdx:170` | Webhook create uses `"secret": "very-secret"`. openapi field is `signing_secret` (`sandbox.mdx:86` already correct). | Rename to `signing_secret`. |

## (c) Organisation / flow problems

| # | Location | Issue | Fix |
|---|----------|-------|-----|
| G1 | `docs.json:4` | Site `name` still `"Starter Kit"` (Mintlify template leftover). | Set to `"Teal"`. |
| G2 | `essentials/*.mdx` (6 files) | Orphan Mintlify template pages, not in nav, irrelevant to the Teal API. Clutter for clients browsing the repo. | Delete. |
| G3 | Nav "Get Started" group | Linear flow OK (`introduction → sandbox → data → authorisations → recurring-checks`). `quickstart` lives under API Reference, slightly disconnected from intro. | Minor; leave order, no structural change this PR. |

## (d) Defects found while auditing (typos / invalid JSON)

| # | Location | Issue | Fix |
|---|----------|-------|-----|
| X1 | `data.mdx:84` | curl header has trailing double quote: `'X-API-KEY: <api-key>''`. | Remove stray `'`. |
| X2 | `sandbox.mdx:695-696` | Response JSON has duplicate `document_filename` key (one `null`, one set) — invalid JSON. | Keep the populated value, drop the `null` duplicate. |

---

## (e) Spec inconsistency in `openapi.yaml`

| # | Location | Issue | Fix |
|---|----------|-------|-----|
| O4 | `api-reference/openapi.yaml` user-level `recurring_check_frequency` | Description listed `[WEEKLY, DAILY]`. Confirmed against payroll-api source: client-supplied `recurring_check_frequency` is validated against `ClientRecurringCheckFrequency` = `WEEKLY, MONTHLY, HOURLY` (HOURLY sandbox-only); `DAILY` belongs to the internal `RetryRecurringCheckFrequency` enum (3am retry cron) and is never a client-selectable value. The user-level field conflated the two enums. | Change to `[WEEKLY, MONTHLY]` (matches client/account-level + content pages + the API validator). |

Note: `openapi.yaml` is hand-maintained directly in this repo (e.g. edited in PR #123), not auto-generated, so the fix is applied here.

## Triage — fixes landing in this PR

In scope (low-risk, high-confidence): **O1, O2, O3, O4, G1, G2, X1, X2**, plus D1/D2 cross-link + field reconciliation.
Deferred: G3 (structural nav rethink — subjective ordering, needs product input).
