---
name: account-handoff-brief
description: >-
  Write a handoff brief when an account changes hands, from sales to CS or between CSMs: what was
  sold and promised, the stakeholder map, open commitments, risk history, and what the customer
  will expect the new owner to already know. Use for handoffs, account transitions, reassignment,
  or taking over an account. Marks what it inferred and turns every gap into a question for the
  previous owner.
---

# Account handoff brief

Handoffs are where context dies. This brief carries over what was sold and promised, who is who, what we owe, what went wrong before, and the things the customer will assume the new owner already knows.

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

1. **Find the account** with `list_accounts`, searching by the name the user gave. Several matches: ask which one. None: say so and stop. **Kind:** if unclear, ask whether this is sales to CS (leans on the deal and sales calls) or a CSM change (leans on account history).
2. **Sold:** `list_account_deals`. **Promised:** `list_call_analyses`, `list_transcript_quotes` (commitments, timelines, preferences, in their words), `list_key_outcomes`, `list_implementation_plans`.
3. **People:** `list_stakeholders`, `read_org_tree`; the owner from `list_accounts` ("no owner recorded" if blank).
4. **What we owe:** `list_action_items` with status `open` and `overdue`; `list_tasks` with states pending, awaiting_approval, approved and in_progress.
5. **History:** `list_tasks` with states done and cancelled, `list_support_tickets` with days=365, `list_friction_items`, `list_signals`, and `search_superbrain` for past escalations and resolved issues.
6. **The relationship:** `list_meetings` with filter past and limit 30, `list_emails` with limit 20, `read_meeting_brief`.

## Write it

In this order:

1. **The account** in three sentences.
2. **What they bought and why.**
3. **Who is who**, with sentiment.
4. **What we owe them**, and by when.
5. **What went wrong before**, and how it was handled.
6. **Things they will expect you to already know:** promises, preferences, sore spots, the names they use.
7. **Open questions for the previous owner:** every gap (no deal, no calls, no email) goes here, not in the narrative.

Mark anything inferred as inferred. If the account is escalating now, lead with that and suggest escalation-brief.

## What this skill does not do

It does not reassign the account or change any record.
