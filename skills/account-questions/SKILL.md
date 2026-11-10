---
name: account-questions
description: >-
  Answer a question about one customer account, or a quick lookup, that no other Superhawk skill
  covers: how an account is doing, what is open, who owns it, its latest emails, tickets, deals or
  meetings, or ranking accounts on one metric. Renewals, business reviews, champion risk,
  expansion, call wrap-ups, escalations, product feedback rollups, onboarding, handoffs and weekly
  digests have their own skills. Reads only the tools the question needs and says plainly when a
  source has no data.
---

# Account questions

The general way to answer a question about a customer from Superhawk. It reads the account first, then only the tools the question needs, and answers that question with a source for each fact. The specialised skills cover the bigger workflows.

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

## Steps

1. **Find the account** with `list_accounts`, searching by the name the user gave. Several matches: ask which one. None: say so and stop. Skip this for a question about the whole book.
2. **Start with `read_account`**, then read only what the question needs:

| The question is about | Read |
|---|---|
| Health and usage | `read_account_health`, then `list_metrics` and `list_metric_snapshots` with the slugs that answer it |
| Goals | `list_key_outcomes`, `list_implementation_plans` |
| Risks | `list_signals`, `list_friction_items`, `list_adoption_blockers`, `read_external_signals` |
| Open work | `list_tasks`, `list_action_items` |
| People | `list_account_owners`, `list_stakeholders`, `read_org_tree` |
| Conversations | `list_meetings`, `read_meeting_brief`, `list_emails`, `list_call_analyses`, `list_transcript_quotes` |
| Support and deals | `list_support_tickets`, `list_account_deals` |
| Anything else | `search_superbrain` |

3. **Ranking or comparing accounts on a metric** uses `list_metric_snapshots` with across_accounts and that metric's slug, never search.
4. **An empty read** is not the answer: read the next relevant source, and say which parts had no data.
5. **Answer the question asked**, briefly, with a source for each fact.

## What this skill does not do

It does not build documents, rank renewals, or draft emails; those have their own skills.
