---
name: expansion-finder
description: >-
  Find expansion opportunities: products or modules an account needs but has not bought, each with
  the gap, the evidence of need and a suggested opener for the account manager. Use when the user
  asks about whitespace, upsell, cross-sell or which accounts are ready to buy more; for upsell
  tied to a coming renewal, renewal-readiness covers it. Uses only product names found in the
  workspace's deals and never quotes a price.
---

# Expansion finder

An expansion opportunity is a gap plus a need: something the account does not own, and evidence it wants what that thing does. This skill finds both, holds back accounts that are not ready, and writes the opener.

## Rules for every read

- **Sources differ by workspace.** Each workspace connects its own CRM, calls, calendar, email, support desk and product metrics. An empty result means that source has no data here: say which, and treat what depends on it as unknown, never as good or bad news.
- **Read each thing once.** Never repeat a call with the same input. A read that comes back empty or with an error is not retried and not replaced by a guess.
- **Widen the defaults.** Tools return little by default (3 email threads, 10 meetings, 90 days of tickets, 20 rows a page). Set limit, days or offset for the window you need, and say what you covered.
- **Metrics by slug.** Get slugs from `list_metrics` with slugs_only. Always pass metric_slug or metric_slugs to `list_metric_snapshots`: without one it returns every metric's full history, too large to use. A null value marked out_of_region is not measured, not zero. Give every number with its date.
- **Health is a band** (Healthy, Moderate, At risk, Not enough data) from `read_account_health`, never a number. Superhawk keeps no history of bands, and `list_signals` returns open signals only.
- **Every fact has a source** (call, email, ticket, deal, signal, metric). Quote people only word for word, from `list_transcript_quotes` or an email.
- **Visible accounts only.** You see the accounts this connection can see. Say so when a total or a ranking depends on it.
- **Never write** an internal id (a UUID), a price, a discount, a date nobody gave you, or a commitment on the customer's behalf. Use only email addresses a tool returned.
- **Change nothing unless asked.** Before any write (`create_task`, `update_task_status`, `create_or_update_signal`), show exactly what you will do and wait for a yes. Nothing here sends email.

## Read the evidence

1. **What is sold:** `list_deals` across the workspace, paging with offset, for the products and modules the deals name. `search_help_articles` only confirms what a module does. Deals name no products: ask the user for the catalog. Never invent a module name.
2. **Scope:** one account (find it with `list_accounts`), or the book via `read_account` without account_id, top 25 by ARR unless the user names others. Say which you covered.
3. **Per account:**
   - what they own: `list_account_deals`
   - what they need: `list_key_outcomes`, `list_friction_items`, `list_adoption_blockers`, `search_superbrain` with a query about requests for things they do not have
   - usage: `list_metrics` with category upsell_signal and slugs_only, then `list_metric_snapshots` per slug (across_accounts to rank the book on one metric)
   - readiness: `read_account_health`, `list_signals`

## What counts

| Account state | Treatment |
|---|---|
| No deal data | cannot tell what they own: no whitespace claimed |
| At risk, or a critical signal | "fix first", not a target |
| Moderate or Not enough data | "qualify first" |
| Healthy, with a gap and a need, both sourced | target now |

## Write it

Per opportunity: the module, the gap, the evidence of need, a size only if the deals or metrics support one, and a two-sentence opener for the account manager. The opener is about the customer's goal, not the product, and goes to the customer: no health, signals, internal metrics, other customers, prices or discounts.

## What this skill does not do

It does not price, quote or create opportunities in the CRM.
