---
name: renewal-readiness
description: >-
  Rank upcoming renewals by risk and give each a specific save or expand play, never a bare red,
  amber or green label. Use when the user asks which renewals to worry about, what renews this
  quarter or in the next N days, for a renewal forecast, or to prep one account's renewal. Treats
  missing data as unknown, and a passed renewal date as something to check, not a churn.
---

# Renewal readiness

Which renewals need attention, in what order, and what to do about each. The ranking comes from health, open risks, sponsor coverage and value delivered; every account gets a concrete play with an owner and a date.

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

1. **Window:** the user's, else the next 120 days. For one account, find it with `list_accounts` and go to step 3.
2. **The book:** `read_account` without account_id. Say how many accounts came back; if the book looks larger, ask the user to narrow by owner or segment. Keep accounts whose renewal date is in the window. List separately, without judging them: no renewal date, and date already passed ("check the CRM": it may have auto-renewed or be stale).
3. **Health:** `read_account_health` with account_ids, in batches.
4. **Per account:**
   - open risks: `list_signals` (`read_signal` for each critical one), `list_adoption_blockers`
   - sponsor: `list_stakeholders`
   - value: `list_key_outcomes`, and `list_metric_snapshots` with the slugs that track those outcomes
   - commercial: `list_account_deals`

## What counts

| Finding | Rule |
|---|---|
| Sponsor risk | a `detractor`, or no `executive_sponsor` or `champion`. No stakeholder data at all: sponsor unknown, not missing |
| Expand candidate | Healthy, no critical signal, and a key outcome met or a usage metric rising |
| Save candidate | At risk or Moderate, or a critical signal, or sponsor risk |
| Not enough data | its own group, never ranked last by default |

**Rank** by band (At risk, Moderate, Healthy), then ARR, then days to renewal.

## Write it

Per account: renewal date, ARR, band, the two or three facts that decide it, and one play: save (what to fix, with whom, by when) or expand (what to offer and why now). More than 15 accounts: full detail for the top 10, one line each for the rest. End with the accounts that had no date or a passed date.

## What this skill does not do

It does not forecast revenue or change a renewal date, and it does not build a QBR; use business-review-builder for that.
