---
name: onboarding-health-check
description: >-
  Review onboarding and implementation progress: stalled milestones, slipping completion, time to
  first value against what was agreed, accounts likely to miss go-live, and the unblock for each,
  labelled customer, internal or executive. Use when the user asks about onboarding health,
  implementation status, go-live risk or stalled onboardings. Judges timing only from dates that
  exist and says when there are none.
---

# Onboarding health check

Onboardings slip one missed milestone at a time. This skill reads each implementation plan against what was agreed in the deal, flags the accounts likely to miss go-live, and names who has to act to unblock each.

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

1. **Scope:** one account (find it with `list_accounts`), or accounts in onboarding: `read_account` with a status filter if the workspace uses an onboarding status, else ask the user which accounts. List accounts with no plan only if the user asked about them.
2. **Plans:** `list_implementation_plans` per account (status, percent complete, milestones, tasks, due dates).
3. **What was agreed:** go-live and success criteria from `list_account_deals` and `list_key_outcomes`.
4. **First value:** `list_metric_snapshots` for the slugs tied to each key outcome, with timeseries.
5. **Causes and owners:** `list_action_items`, `list_adoption_blockers`, `list_stakeholders`, `list_tasks`.

## What counts

| Finding | Rule |
|---|---|
| Stalled | a milestone or task past its due date and not done |
| Slipping | percent complete well behind the share of milestones already due |
| Go-live date | the deal's, else the last milestone's due date. Neither: skip the go-live test and say so |
| Likely to miss go-live | stalled before go-live, or slipping with under 30 days to it |
| First value | the first key outcome whose metric moved. None moved: say so |
| No due dates | timing cannot be judged; say so |

Plans carry no separate start or target date: judge timing from milestone due dates only.

## Write it

In order: likely to miss go-live, then stalled, then on track in one line each. Per flagged account, the unblock, labelled by who must act:

- **Customer:** what to ask, and of whom.
- **Internal:** which team; check `list_tasks` for one already open.
- **Executive nudge:** why, and from whom.

## What this skill does not do

It does not change plans, milestones or tasks.
