---
name: voice-of-customer
description: >-
  Roll up what customers are saying across the book for the product team: friction and adoption
  blockers clustered by theme, weighted by ARR and severity, with representative quotes and a
  concrete ask per theme. Use when the user wants voice of the customer, a product feedback
  rollup, top pain points or themes across accounts. Counts accounts, not mentions, and quotes
  customers only word for word.
---

# Voice of the customer

What the whole book is telling the product team, ranked by how much revenue and pain sits behind each theme. Dense, metric-heavy, and ending in asks the product team can act on.

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

1. **Friction and blockers:** `list_friction_items` and `list_adoption_blockers` without account_id, per_page 100, paging until a page comes back short; add severity or type filters if the user asks. Stop at 10 pages and say what was cut.
2. **Both empty:** check `list_call_analyses`. No calls means no call source; calls with nothing extracted means nothing to roll up from calls. Then use `list_support_tickets` (days=90, limit 200) and `search_superbrain`, and say the rollup covers only those.
3. **ARR and owner per account:** `read_account` without account_id.
4. **Quotes:** `list_transcript_quotes` with the theme's keywords for the two largest accounts in it, customer lines only.

## What counts

| Rule | Why |
|---|---|
| Cluster by what the customer is trying to do | our feature names hide the job |
| Count accounts, not mentions | one noisy account does not make a theme |
| Weight by total ARR and highest severity | revenue and pain together |
| Accounts with no ARR listed separately | never weighted as zero |
| Covered by a ticket: friction items carry has_ticket_ref; for blockers match tickets yourself or leave it blank | the product team sees what support already knows |

## Write it

A ranked table first (theme, accounts, ARR, top severity, ticketed). Then one paragraph per top theme: what customers are trying to do, two quotes word for word, and one concrete ask for the product team (fix, build, document or investigate) with what it would unlock. No call source: no quotes, and ticket text is summarised, not quoted.

## What this skill does not do

It does not file tickets or product requests, and it writes nothing back to Superhawk.
