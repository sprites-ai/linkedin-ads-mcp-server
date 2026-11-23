# LinkedIn Ads MCP Server

Connect LinkedIn Campaign Manager to Claude, ChatGPT, Cursor and any MCP client. Cost per lead by segment, ABM account lists, audience forecasts and new campaigns, with every change approved by you first.

![License: MIT](https://img.shields.io/badge/license-MIT-blue) ![MCP: remote](https://img.shields.io/badge/MCP-remote-6E56CF) ![Transport: streamable HTTP](https://img.shields.io/badge/transport-streamable%20HTTP-informational) ![Auth: OAuth](https://img.shields.io/badge/auth-OAuth-success) ![Hosted by Sprites](https://img.shields.io/badge/hosted%20by-Sprites-111111)

**Server URL:** `https://mcp.sprites.ai/mcp` · streamable HTTP · OAuth sign-in · 6 read tools · 14 write tools · every write approved by you

> This repository holds the documentation, client configs and MCP Registry metadata for the hosted
> LinkedIn Ads MCP server run by [Sprites](https://www.sprites.ai). The server itself is hosted by Sprites and its source is not
> in this repository. There is nothing to install or run.

## What's new

| Date | Change |
|---|---|
| 2026-11-23 | Initial public release of connector docs and configs |

## Works with

- **Claude**: claude.ai and Claude Desktop, custom connector (renders approval cards)
- **Claude Code**: `claude mcp add`
- **ChatGPT**: developer mode app (Plus, Pro, Business, Enterprise, Edu) (renders approval cards)
- **Codex**: `codex mcp add` or `config.toml`
- **Cursor**: `mcp.json`
- **Gemini CLI**: `settings.json`
- **GitHub Copilot (VS Code)**: `.vscode/mcp.json`, agent mode
- **Google Antigravity**: `mcp_config.json`
- **Windsurf**: `mcp_config.json`
- **Lovable**: Connectors, Custom MCP
- **Grok**: grok.com/connectors, Custom
- **Cline**: Remote Servers, Streamable HTTP
- **Devin**: Customize, MCPs
- **Replit Agent**: Integrations, MCP Servers

Any other client that speaks remote MCP over streamable HTTP with OAuth should work with the same URL.

## Quick start

1. **Connect LinkedIn Ads in Sprites.** Sign up at [sprites.ai](https://www.sprites.ai) and connect LinkedIn Ads once in the Sprites integrations menu. Plans start at $29 a month with a 7-day trial for $1. There is no free plan.
2. **Add the server to your client** with the URL `https://mcp.sprites.ai/mcp` (one endpoint for every client and every channel).
3. **Sign in.** Your client opens the Sprites consent screen. Sign in with the account your channels are connected to and approve.
4. **Ask.** Try: "With Sprites, what is our LinkedIn CPL by job function this quarter?"

## Setup by client

### Claude (claude.ai and Claude Desktop)

Works on Free (one custom connector), Pro, Max, Team and Enterprise.

1. Settings, then Customize, then Connectors. On Team and Enterprise an Owner adds it once under Organization settings, Connectors.
2. Click **+**, then **Add custom connector**. Name it `Sprites` and paste `https://mcp.sprites.ai/mcp`. Leave Advanced settings empty.
3. Click **Connect**, sign in to Sprites and approve.

Claude draws the Sprites approval card in the chat, so you can edit a budget or a headline in place and approve it.

### Claude Code

```bash
claude mcp add --transport http sprites https://mcp.sprites.ai/mcp
```

Then type `/mcp` inside Claude Code to finish the browser sign-in.

### ChatGPT

Plus, Pro, Business, Enterprise and Edu, on the web app. Not available on Free.

1. Settings, Security and login: turn on **Developer mode**. On Business and Enterprise an admin has to allow it first.
2. Settings, **Plugins** (formerly Connectors), then **+** to create an app.
3. Name `Sprites`, MCP server URL `https://mcp.sprites.ai/mcp`, Authentication **OAuth**. Tick the trust box and click Create.
4. Connect the app and sign in to Sprites.

### Cursor

`~/.cursor/mcp.json` (every project) or `.cursor/mcp.json` (one repo):

```json
{
  "mcpServers": {
    "sprites": {
      "url": "https://mcp.sprites.ai/mcp"
    }
  }
}
```

Cursor discovers the Sprites authorization server from the endpoint and opens a browser to sign in.

### Codex

```bash
codex mcp add sprites --url https://mcp.sprites.ai/mcp
codex mcp login sprites
```

Or by hand in `~/.codex/config.toml`:

```toml
[mcp_servers.sprites]
url = "https://mcp.sprites.ai/mcp"
auth = "oauth"
```

### VS Code and GitHub Copilot

`.vscode/mcp.json`. The top-level key is `servers`, not `mcpServers`, and a remote server needs `"type": "http"`:

```json
{
  "servers": {
    "sprites": {
      "type": "http",
      "url": "https://mcp.sprites.ai/mcp"
    }
  }
}
```

Run **Auth** above the entry, sign in, then switch Copilot Chat to **Agent** mode. Ask mode does not call MCP tools.

### Gemini CLI

`~/.gemini/settings.json`. Use `httpUrl`; a `url` entry is treated as SSE:

```json
{
  "mcpServers": {
    "sprites": {
      "httpUrl": "https://mcp.sprites.ai/mcp"
    }
  }
}
```

Restart the CLI; the first call opens the sign-in. Check with `gemini mcp list`.

### Other clients

| Client | How |
|---|---|
| Windsurf | `~/.codeium/windsurf/mcp_config.json` with `"serverUrl": "https://mcp.sprites.ai/mcp"` |
| Google Antigravity | `~/.gemini/config/mcp_config.json` with `"serverUrl": "https://mcp.sprites.ai/mcp"` (`url` and `httpUrl` are skipped) |
| Cline | Remote Servers tab, transport Streamable HTTP; in the file, `"type": "streamableHttp"` |
| Lovable | Connectors, Custom MCP, paste the URL |
| Grok | grok.com/connectors, New Connector, Custom (paid tiers) |
| Devin | Customize, MCPs, Add custom MCP, transport HTTP, authentication OAuth |
| Replit Agent | Integrations, MCP Servers, Add MCP server, Test & Save |

Copy-ready files for each are in [`configs/`](configs/).

## What it can read

- Campaigns, campaign groups, creatives and audiences
- Spend, impressions, clicks, reach, leads, CPL and conversions
- Demographic breakdowns: job function, seniority, company, industry
- LinkedIn's own forecast: audience size, cost per lead, CPC and CTR
- Uploaded company lists and matched audiences

## What it can change (with your approval)

- Create campaigns, audiences, matched company lists and creatives
- Budgets, schedules, bids, targeting and status
- Lead gen campaigns with the form attached
- New campaigns are created paused

Every change arrives as an approval with the before and after. In Claude and ChatGPT it is a card you can edit
in place. In clients without cards, Sprites returns the change written out with a gate id, and applies it only
after you confirm (`approve_change`). Nothing is applied by the model on its own.

## Permissions

- **OAuth.** You connect LinkedIn once inside Sprites. Sprites requests `r_ads`, `rw_ads`, `r_ads_reporting`, `rw_conversions`, `r_organization_admin`, `r_organization_social`, `w_organization_social`, `w_member_social` and `r_marketing_leadgen_automation`, plus `openid profile email`.
- **Advertising API access.** LinkedIn approves each developer app before it can reach an ad account. Sprites runs on the official Advertising API through its own approved access, so you do not register a developer app or wait for review.
- **Not a browser bot.** Sprites does not drive a logged-in LinkedIn session, which LinkedIn's User Agreement prohibits.
- **Account access.** Sprites acts with the role your LinkedIn member has on the Campaign Manager account. A viewer role can be reported on but not changed.
- **Your AI client never holds a LinkedIn credential.** It holds a Sprites OAuth token bound to `https://mcp.sprites.ai/mcp`.

## Tools

The server exposes 20 tools to the model: 6 read tools and 14 write tools. Write tools that touch an ad account or a site only propose; nothing reaches either until you approve it. The tools that do the LinkedIn Ads work:

| Tool | What it does on LinkedIn Ads |
|---|---|
| `linkedin_ads_report` | Spend, leads, CPL, CTR and reach by campaign and segment. Read-only. |
| `linkedin_forecast` | LinkedIn's forecast for up to 5 audiences: members, cost per lead, CPC and CTR over 30 days. Creates nothing. |
| `linkedin_ads_campaign` | A new Sponsored Content, lead gen, message or retargeting campaign, built end to end. |
| `linkedin_abm_campaign` | Target-account lists, company matched audiences, buying-committee targeting. |
| `linkedin_ads` | Everything else on LinkedIn by intent: audit, budget, pause, targeting, creative, competitors, recurring reports. |
| `schedule_task` | A recurring LinkedIn report or CPL monitor that runs inside Sprites. |

LinkedIn Ads requests use `channel: linkedin_ads`. Name another channel, or `all`, to compare.

Every task tool takes the same input: `goal` (the request written to stand on its own: Sprites sees only this
string, not the chat), `title` (a short label), `channel`, and `today` (the user's local date, so "last 30 days"
resolves correctly). Long tasks return a run id; `task_status` collects the result and `cancel_task` stops it.

<details>
<summary>Full tool list (20 tools, plus <code>approve_change</code> on clients without cards)</summary>

| Tool | Type | What it does |
|---|---|---|
| `analyze` | read | Performance, reports, spend, revenue and Shopify sales questions, period comparisons, diagnosing a drop. Reads only. |
| `research` | read | Competitors and their ads, keyword research (paid or SEO), market and audience research, AI visibility. |
| `optimize` | write | Audits, wasted spend, scaling winners, moving budget. Reads, reports, and queues the recommended changes as approval cards. |
| `create_creative` | write | Ad copy, headlines and descriptions, and written concepts for image or video ads. Writes creative; does not generate images, video or audio. |
| `launch` | write | New campaigns, ad sets or ad groups, audiences, retargeting, duplicating a campaign, A/B tests, proposed as approval cards. |
| `change` | write | One named edit: budget, pause or enable, keywords or negatives, targeting, rename. Opens one approval card. |
| `schedule_task` | write | Recurring work that runs inside Sprites: weekly briefs, monitors, alerts, recurring content. |
| `write_seo_blog_post` | write | A researched blog post for your site, drafted in your CMS and published once approved. |
| `create_seo_page` | write | Create or edit a landing or service page, meta titles and descriptions, schema; a diff card before publishing. |
| `create_aeo_content` | write | Content meant to be cited by ChatGPT, Perplexity, Gemini or AI Overviews: answer, FAQ and comparison pages, llms.txt. |
| `run_task` | write | Anything else: setup, capability questions, cross-channel and multi-step jobs. |
| `linkedin_ads_report` | read | LinkedIn Ads performance: spend, leads, CPL, CTR, reach, period comparisons. |
| `linkedin_forecast` | read | LinkedIn's own forecast for up to 5 audiences: members, cost per lead, CPC and CTR over 30 days. Creates nothing. |
| `linkedin_ads_campaign` | write | Create a LinkedIn campaign: Sponsored Content, lead gen forms, message ads, retargeting. |
| `linkedin_abm_campaign` | write | LinkedIn account-based marketing: target account lists, company matched audiences. |
| `linkedin_ads` | write | Any other LinkedIn Ads task, routed by intent (audit, budget, pause, targeting, creative). |
| `account_status` | read | Which channels are connected in Sprites and how many credits are left. |
| `choose_business` | write | Select which Sprites business the tools work on, by name, or list your businesses. |
| `task_status` | read | Status and result of a Sprites task by its run id. |
| `cancel_task` | write | Stop a running task. |
| `approve_change` | write | Only on clients that do not render the approval card (Codex, Cursor, Claude Code and others): apply or decline a pending change by its gate id, after you confirm it. |

</details>

## Example prompts

1. With Sprites, what is our LinkedIn CPL by job function this quarter?
2. Use Sprites to build an ABM campaign for Series B fintechs in the US, 200 to 1000 employees.
3. Ask Sprites which LinkedIn audiences are saturated and should be rotated.
4. Have Sprites draft 3 new Sponsored Content intros for our best-performing lead gen form.
5. With Sprites, compare LinkedIn spend and leads this month against last month.
6. Use Sprites to forecast LinkedIn reach and cost per lead for VP Finance at US companies with 1000+ staff.
7. Ask Sprites to compare forecasts for three audiences: CFOs, Controllers, and Heads of FP&A in the UK.
8. Have Sprites upload this list of 200 target accounts as a LinkedIn company audience.
9. With Sprites, cut the LinkedIn budget on campaigns with CPL above $250 by 20%.
10. Use Sprites to send me a LinkedIn leads and CPL report every Friday.

More in [`examples/prompts.md`](examples/prompts.md). Saying "with Sprites" or "use Sprites" helps clients that
rank their own built-in tools first (Cursor, Grok).

## Official server vs this one

**LinkedIn (no MCP server for MCP clients).** LinkedIn publishes no MCP server that Claude, Cursor or another MCP client can connect to. Its only first-party option is the LinkedIn Ads app inside ChatGPT, a read-only beta for ad set performance. Open-source servers on the Advertising API need your own LinkedIn developer app, which LinkedIn must approve; the most-starred LinkedIn servers on GitHub drive a logged-in browser session instead.

| | Official / self-hosted | Sprites (this server) |
|---|---|---|
| Official MCP server | None for MCP clients; a read-only app inside ChatGPT only | Hosted, works in any MCP client |
| API access | Your own developer app, approved by LinkedIn | Sprites' approved Advertising API access |
| Writes | Nothing (official ChatGPT app) | Campaigns, audiences, company lists, budgets, bids, creatives, each an approval card |
| Forecasts | Not offered | LinkedIn's own audience size and cost forecast, in seconds |

There is no first-party LinkedIn server to compare against outside ChatGPT. If you want to run one yourself, pick a server on the Advertising API, not one that drives a browser.

## FAQ

**Is this a server I have to host?**
No. It is a hosted remote MCP server. Paste `https://mcp.sprites.ai/mcp` into your client and sign in.

**Is there an official LinkedIn Ads MCP server?**
Not one that MCP clients can connect to. LinkedIn's only first-party option is a read-only LinkedIn Ads app inside ChatGPT.

**Is this allowed by LinkedIn?**
Yes. Sprites uses the official LinkedIn Advertising API through approved access. It does not automate a browser session.

**Can it forecast an audience before I spend?**
Yes. `linkedin_forecast` returns LinkedIn's own forecast for up to 5 audiences (members, cost per lead, CPC and CTR over 30 days) from your connected ad account, and creates nothing.

**Can it build an ABM campaign?**
Yes. Give it target accounts (a CSV or a list) or an ICP and it builds the matched company audience and a campaign, staged as approval cards and created paused.

**Why do I not see the LinkedIn tools?**
In Claude and ChatGPT, the LinkedIn tools are left off the tool list for an account with no LinkedIn connected. Connect LinkedIn in Sprites and refresh the connector.

**Does it cover other channels?**
Yes. The same URL reaches Google Ads, Meta Ads, LinkedIn Ads, TikTok Ads, Reddit Ads, Microsoft Advertising,
Apple Ads, GA4, Search Console, Shopify, SEO and AI visibility, and Google Business Profile, for whatever is
connected in Sprites. See [ads-mcp-servers](https://github.com/sprites-ai/ads-mcp-servers).

**How much does it cost?**
The connector is part of your Sprites plan: Build is $29 to $99 a month, Launch is $300 to $500 a month, and
larger companies get a custom plan. Every plan starts with a 7-day trial for $1. LinkedIn Ads charges its own media
cost as usual.

**The client says it has no tools, or every call is unauthorized.**
Remove the server, re-add it with exactly `https://mcp.sprites.ai/mcp`, and sign in again. Grants are bound to that one URL.

**How do I revoke access?**
Disconnect the connector in your client, or remove the connection in Sprites. The next tool call fails closed.

## Maintained by Sprites

This server is built and run by [Sprites](https://www.sprites.ai), an AI marketing agent for paid ads, SEO and AI visibility.

- Product page: [sprites.ai/mcp/linkedin-ads](https://www.sprites.ai/mcp/linkedin-ads)
- Setup guide: [sprites.ai/linkedin-ads-mcp](https://www.sprites.ai/linkedin-ads-mcp)
- All Sprites MCP servers: [github.com/sprites-ai](https://github.com/sprites-ai)
- Issues with these docs or configs: open an issue here. Security reports: see [SECURITY.md](SECURITY.md).

Licensed under the [MIT License](LICENSE). The license covers the documentation and configuration files in this repository.
