---
name: escalation-brief
description: >-
  Get the user up to speed fast on an escalating account: a one-page timeline, what the customer
  is really upset about versus noise, a response plan and who should be in the room. Use when the
  user says an account is escalating, angry, threatening to churn, or needs urgent attention.
  Separates the real issue by evidence, never by sentiment alone, and promises nothing on our
  behalf.
---

# Escalation brief

When an account escalates, the first hour decides it. This skill builds the timeline, finds the issue that actually matters, and gives a response plan and who should be in the room, on one page.

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

1. **Find the account** with `list_accounts`, searching by the name the user gave. Several matches: ask which one. None: say so and stop. **Window:** the last 60 days unless the user says otherwise.
2. **Tickets:** `list_support_tickets` with days=60; include_messages only for the 10 most recent or most severe.
3. **Friction and signals:** `list_friction_items`, `list_signals` (`read_signal` for each critical one). These have no date filter: drop items dated outside the window and list undated ones separately.
4. **Conversations:** `list_emails` with limit 20, `list_meetings` with filter past and limit 30.
5. **Their words:** `list_transcript_quotes` with keywords from the complaint, customer lines only.
6. **Who matters:** `list_stakeholders`. **Commitments made:** `list_tasks`, `list_action_items`.

## What counts

| Item | Treatment |
|---|---|
| Appears in more than one source, or more than once | real issue |
| Any single item from an executive or sponsor | real issue |
| An explicit churn, cancellation or legal threat | real issue, said first |
| One-off, resolved, or sentiment with no quote behind it | noise, listed briefly so nothing looks hidden |

## Write it

One page, oldest to newest:

1. **Three-sentence summary** of the situation.
2. **Timeline:** one line per event, with date and source.
3. **The real issue** and the noise, per the table.
4. **Response plan:** what to say first, what to commit to and by when, what not to promise, and any commitment we already missed.
5. **Who should be in the room:** the account owner from `list_accounts`, and by role support, product or an executive, depending on the issue. Name people only if a tool returned them.

If the user is also taking over the account, suggest account-handoff-brief next.

## What this skill does not do

It does not reply to the customer, page anyone, or record a signal unless asked.
