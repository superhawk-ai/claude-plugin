---
name: champion-risk-watch
description: >-
  Find relationship risk: accounts that are single-threaded, have no executive sponsor, or whose
  champion has gone quiet or may have left, and propose a multithreading plan naming the missing
  personas and who could introduce them. Use when the user asks about champion or sponsor risk,
  single-threaded accounts, relationship coverage, or who has gone quiet. Never calls a contact
  quiet when the source that would show contact is not connected.
---

# Champion risk watch

Relationships fail quietly: one contact, a sponsor who stopped replying, a champion who left. This skill checks who we know, when we last spoke to them and what changed at their company, then says who to add and how.

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

1. **Scope:** one account (find it with `list_accounts`), or the book via `read_account` without account_id, highest ARR first. Above 25 accounts, stop and say how many you checked. `read_org_tree` without account_id lists the least-covered accounts first, where the org tree is on.
2. **Per account:**
   - who matters: `list_stakeholders` (sponsor status, sentiment, quotes), `read_org_tree` (personas nobody fills)
   - last contact per key person: `list_call_analyses` (people on each call), `list_meetings` with filter past and limit 50, `list_emails` with limit 20. Say when a person cannot be matched to any contact.
   - change at their company: `read_external_signals` (leadership changes, headcount, hiring)

## What counts

| Flag | Evidence |
|---|---|
| Single-threaded | only one of their people appears in calls, meetings or email in the last 90 days |
| No executive sponsor | no `executive_sponsor` among stakeholders |
| Gone quiet | no call, meeting or email with a champion or sponsor in 45 days, or the user's threshold. Calls, calendar and email all unconnected: contact unknown, not quiet |
| May have left | a leadership change or departure signal naming them; "may have left" unless a source confirms it |
| Turned | sentiment `negative` or `very_negative`, or sponsor status `detractor` |

No stakeholder or org data: coverage unknown. Say so instead of flagging.

## Write it

Per flagged account: the flags with their evidence, the personas missing, who could introduce each (a current contact, their executive, our executive, the account owner), and the first message to send. Name people only if a tool returned them; otherwise name the role.

## What this skill does not do

It does not contact anyone or change stakeholder records.
