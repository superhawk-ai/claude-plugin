---
name: post-call-wrap-up
description: >-
  Wrap up a customer call: draft the recap email for the user to send and, once the user approves
  the list, create our follow-up tasks with due dates. Use right after a call, or when the user
  says wrap up, recap or follow up on a call. Never sends anything, never invents a due date or an
  email address, and creates nothing without a yes.
---

# Post-call wrap-up

The two things a CSM owes after a call: a recap the customer can act on, and our own follow-ups tracked in Superhawk. This skill drafts the first and, after approval, creates the second.

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

1. **Find the account** with `list_accounts`, searching by the name the user gave. Several matches: ask which one. None: say so and stop.
2. **The call:** `list_call_analyses` for the account, newest first; if the user named a day, narrow with date_from and date_to, and ask if several match. Read it in full with its call_id. Not there yet (analysis can lag the call): say so and offer to work from notes the user pastes.
3. **From the analysis:** decisions, the call's action items (owner `csm`, `customer` or `shared`), friction, attendees.
4. **Older commitments worth repeating:** `list_action_items` for the account, status `open` and `overdue`.
5. **Exact words:** `list_transcript_quotes` for the account, keeping lines from that call's date.
6. **Recipients:** addresses from `list_emails` threads with this account only.

## Write the recap

- **Point first:** what was agreed. Then next steps with owners and dates, then open questions.
- **Every agreed next step goes in**, ours included. An item with no date gets none: ask the user rather than inventing one.
- **Short:** three short paragraphs or fewer, one explicit ask.
- **Recipients:** only addresses a tool returned. None found: leave them empty and say "add the recipient".
- **It goes to the customer.** Leave out health bands, signals and their wording, churn or upsell metrics, deal qualification fields and what they are replacing, sponsor and sentiment labels, recording links, internal notes, other customers, and anything only our team said. Tell the user, separately, what you left out.

## Propose the tasks

Show them as a list before creating anything:

- **One per action item we own.** A shared item only if the user wants it. Check `list_tasks` for duplicates first.
- **Each task:** title, rationale (what was said, on which call), labels such as source:call, and a due date as a task payload with due_at.
- **Blocked on another team** (product, support, implementation, finance): set waiting_on to that team. This marks the blocker; it does not assign anyone.

**Create only after a yes**, with `create_task`. Tasks go to the account's owner; there is no per-task assignee. Check the owner in `list_accounts` first and warn if there is none. A refused write (no write permission on this connection): give the list to create by hand.

## What this skill does not do

It does not send the email, assign a task to a person, or change a task's state.
