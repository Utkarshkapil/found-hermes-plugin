# FOUND for Hermes

Run [FOUND][found], the AI SDR for LinkedIn and email, from
[Hermes Agent](https://hermes-agent.nousresearch.com). Ask Hermes who your
warmest leads are and why, how your campaigns are doing, what FOUND knows about
an account, or to set up a new campaign. It works with your existing
[FOUND][found] account.

```
You:    Who are my warmest leads this week, and why?

Hermes: 6 people. The top 3:
        1. Head of Growth at a 40-person SaaS. Posted "our outbound reply rate
           fell off a cliff once we scaled sends" on Tuesday.
        2. Co-founder, seed-stage fintech. Raised $4M last week; commented on
           a post about hiring a first SDR.
        3. VP Sales, logistics software. Engaged with two posts about Apollo.
        Want a draft opener for the first one?
```

FOUND finds people from buying signals (someone posting about the problem you
solve, engaging with a competitor, starting a new role, a new funding round),
checks each one against your buyer profile and writes every message from what
that person did.

This is an [Agent Plugins v1](https://agent-plugins.org/specification) package
with no executable code of its own:

| File | What it does |
| --- | --- |
| `plugin.json` | Package manifest. Marks the FOUND server `untrusted`, so Hermes asks before any tool that writes or spends |
| `mcp.json` | Connects to FOUND's hosted MCP server at `https://app.foundtofind.com/mcp` |
| `skills/found-outreach/SKILL.md` | Which tool answers which question, what needs confirmation, and what the tools cannot do |

## Requirements

- Hermes Agent 0.21.6 or newer
- Node.js 20.18.1 or newer, with `npx` on your `PATH`
- A [FOUND][found] account: [$69 a month][pricing]

## Install

```bash
hermes plugins install Utkarshkapil/found-hermes-plugin --no-enable
hermes plugins enable found
```

Start a new session. The first time the server starts, your browser opens
FOUND's sign-in page. Sign in and approve. The token is kept and refreshed, so
you sign in once.

If the tools do not appear after you sign in, run `/reload-mcp`.

### Use a key instead of the browser sign-in

On a server or another headless machine, skip the plugin and add the server to
`~/.hermes/config.yaml` with a key from FOUND (**Settings → Connect →
Advanced**):

```yaml
mcp_servers:
  found:
    url: "https://app.foundtofind.com/mcp"
    headers:
      Authorization: "Bearer ${FOUND_API_KEY}"
```

Put `FOUND_API_KEY=...` in `~/.hermes/.env`. A key works like a password for
your FOUND account: do not paste it into a chat.

## What you can do

| Area | Tools |
| --- | --- |
| Account | `connect_status`: plan, LinkedIn and mailbox health, campaigns, lists, credits |
| Leads | `find_leads`: warm leads FOUND already found, with the signal behind each |
| Numbers | `pipeline_stats`, `pipeline_breakdown`, `campaign_report`, `compare_campaigns` |
| Accounts | `prioritise_accounts`, `research_account` |
| New people | `find_new_leads`, `find_contact_at_company`, `get_new_leads`, `enrich_lead` (credits, price shown first) |
| Campaigns | `get_campaign`, `create_campaign` (paused), `update_sequence` (paused only), `add_to_list`, `add_to_campaign` (paused only) |
| Messages | `draft_message`: the message FOUND would send one lead, as a draft |

The server has 18 tools. The tool list comes from the hosted server at run
time, so it can change without a new release of this plugin.

## What it will not do

- **Nothing sends from Hermes.** No tool sends an invitation, message or
  email. Campaigns made from Hermes start paused; you switch them on in FOUND,
  where FOUND's pacing, daily caps, block list and opt-outs apply.
- **No surprise spending.** A tool that uses credits answers first with the
  price, and spends only when called again with that price confirmed.

## Disclosure

- Every tool call goes to FOUND's hosted server (`app.foundtofind.com`) and
  acts on your FOUND account. The tool set is served by that server at run time
  and is not covered by the pinned commit.
- Sign-in runs the pinned `mcp-remote@0.1.49` through `npx`, which opens a
  browser and a local callback port on first use and stores OAuth tokens under
  `~/.mcp-auth`.
- The package marks the server `untrusted`, so Hermes asks before every tool
  the server does not mark read-only, and refuses those in unattended runs such
  as cron jobs.
- Results include text other people wrote (profiles, posts, messages, news).
  The server labels those fields, and the bundled skill treats them as data.

## Support

[foundtofind.com/mcp](https://foundtofind.com/mcp) ·
[utkarsh@foundtofind.com](mailto:utkarsh@foundtofind.com)

[found]: https://foundtofind.com
[pricing]: https://foundtofind.com/pricing
