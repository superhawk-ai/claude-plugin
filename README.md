# Superhawk for Claude

Superhawk is a customer success platform. This plugin connects Claude to your Superhawk workspace so you can review accounts, health, goals, risks and open work from a conversation.

## What it includes

- **Superhawk connector** (`https://mcp.superhawk.ai/mcp`): read accounts, health, key outcomes, stakeholders, meetings, emails, support tickets, call analyses and tasks; create tasks and signals when you ask.
- **Skills** that tell Claude how to use those tools for common customer success work:

| Skill | Ask something like |
|---|---|
| account-questions | "How is Acme doing?", "What is open with Acme?" |
| renewal-readiness | "Which renewals should I worry about this quarter?" |
| business-review-builder | "Build the Q3 QBR for Acme" |
| champion-risk-watch | "Which accounts are single-threaded?" |
| expansion-finder | "Where can we expand in my book?" |
| post-call-wrap-up | "Wrap up my call with Acme" |
| escalation-brief | "Acme is escalating, get me up to speed" |
| voice-of-customer | "What are customers struggling with most?" |
| onboarding-health-check | "Which onboardings are at risk of missing go-live?" |
| account-handoff-brief | "I'm taking over Acme, brief me" |
| weekly-book-digest | "Give me this week's book of business digest" |

Every skill works with whichever sources your workspace has connected, and says plainly when a source has no data. Skills only read, except post-call-wrap-up, which creates tasks after you approve them. None of them sends email.

## Setup

1. Install the plugin.
2. Connect the Superhawk connector from the plugin's Connectors tab.
3. Sign in with your Superhawk account, pick a workspace and approve access.

You need a Superhawk account. Every call runs as you, with the same permissions and account scope you have in the Superhawk dashboard.

## Data

The plugin sends your questions and tool arguments (such as an account name) to Superhawk at `mcp.superhawk.ai`, which returns data from your own workspace. Data stays in your workspace's region (US or EU). The plugin itself stores nothing and contacts no other service.

## Privacy

See the Superhawk privacy policy: https://superhawk.ai/privacy

## Support

Email support@superhawk.ai.
