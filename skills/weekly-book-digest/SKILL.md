---
name: weekly-book-digest
description: >-
  Produce a weekly book-of-business digest for CS leaders, per CSM: current health, open serious
  signals, overdue action items and renewals at risk, ready for 1:1s or a Monday pipeline review.
  Use when the user asks for a weekly digest, team rollup, book of business summary or CSM review;
  for a deeper look at renewals alone, use renewal-readiness. Never claims a health band changed
  without dated evidence.
---

# Weekly book digest

The book of business a CS leader reads every week without logging in: per CSM, what is at risk, what is overdue and what renews soon, with one talking point for each 1:1.

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

1. **Owners:** `list_account_owners` with limit 100, paging with offset until done.
2. **Accounts:** `read_account` without account_id, for ARR and renewal date. Group by owner; an account under no owner goes under "Unassigned".
3. **Health:** `read_account_health` with account_ids, in batches.
4. **Window:** the last 7 days unless the user says otherwise.
5. **Serious signals:** `list_signals` only for At risk accounts and accounts renewing within 90 days; keep `critical` and `warning`.
6. **Overdue action items:** `list_action_items` without account_id, status `overdue`, then status `open` with due_before today, paging both and removing duplicates.
7. **What happened:** `search_superbrain` with since set to the window start.

## What counts

| Item | Rule |
|---|---|
| New signal | only one that carries a date in the window; otherwise "open" |
| Band change | never claimed: Superhawk keeps no band history, unless a dated signal or fact in the window says so |
| Renewal at risk | renewal within 90 days and band At risk or Moderate; Not enough data listed as unknown |
| Overdue item owned by the customer | its own line, not counted against the CSM |

## Write it

A team table first (per CSM: accounts, ARR, At risk, renewals in 90 days, overdue items). Then per CSM: a one-line headline, at-risk renewals, serious signals, overdue items, and one talking point for the 1:1. Say whose accounts this connection can see.

## What this skill does not do

It does not message the team or change any account.
