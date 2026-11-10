---
name: business-review-builder
description: >-
  Build a QBR or EBR for one customer account: outcomes against success criteria, metric trends
  over the period, customer quotes, open risks, and the framing of what to lead with, what to own
  and what to ask for. Use when the user wants a QBR, EBR, business review or executive review, as
  a deck or an outline. Customer-facing: keeps internal data out and quotes customers only word
  for word.
---

# Business review builder

A business review is the customer's story of the period, told with their numbers and their words. This skill gathers the evidence, decides the framing, and produces a deck or slide-by-slide content that is safe to show the customer.

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

1. **Find the account** with `list_accounts`, searching by the name the user gave. Several matches: ask which one. None: say so and stop. **Period:** the user's, else the last full quarter.
2. **Goals and success criteria:** `list_key_outcomes`, `list_account_deals`, `list_implementation_plans`.
3. **Results:** `list_metrics` with slugs_only and category base_metric or derived, then `list_metric_snapshots` per chosen slug with timeseries and lookback_days counted from today back to the period start. Keep only rows inside the period; show start, end and change.
4. **Their words:** `list_transcript_quotes` for the account, keeping lines marked as the customer's and dated in the period. No call data: no quotes section, never a paraphrase presented as a quote.
5. **Risks and open work:** `list_signals`, `list_friction_items`, `list_action_items`, `list_support_tickets` with days set to the period.
6. **Who will attend:** `list_stakeholders`.

## Frame it

- **Lead** with the outcome the customer cares most about that moved.
- **Own** every miss: its cause and the fix already in motion. Present risks as "what we are working on together", never as internal signals.
- **Ask** for one to three things (expansion, executive time, a reference, a renewal step), each tied to evidence.
- **Value by module** only if the workspace tracks per-module metrics; otherwise show value by outcome and say so.

## Write it

**It goes to the customer.** Leave out health bands, signals and their wording, churn or upsell metrics, deal qualification fields and what they are replacing, sponsor and sentiment labels, recording links, internal notes, other customers, and anything only our team said. Tell the user, separately, what you left out.

If you can create files and the user wants a deck, build it. Otherwise give slide-by-slide content: title, the one message, the evidence, speaker notes.

## What this skill does not do

It does not send anything, invent targets the customer never agreed, or show internal health.
