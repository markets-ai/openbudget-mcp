# OpenBudget MCP Server

**Your real bank accounts, balances, and spending — now available to your AI assistant.**

OpenBudget is a hosted [Model Context Protocol](https://modelcontextprotocol.io) (MCP) server that gives Claude, ChatGPT, Cursor, Codex, and any other MCP-capable client access to your actual financial data — transactions, balances, net worth, credit cards, loans, and mortgages — synced from 10,000+ US institutions through Plaid. You can also add off-bank money it can't see, like physical cash or a gift-card balance, and ask about everything together.

You don't run anything. The server is hosted on OpenBudget's infrastructure. You add one URL to your client, sign in once, and start asking.

```
https://api.openbudget.sh/mcp
```

- **Transport:** Streamable HTTP (remote MCP server)
- **Auth:** OAuth 2.0 — sign in with your OpenBudget account
- **Plan:** requires an [OpenBudget subscription](https://openbudget.sh/pricing) — $5/month billed annually, or $9.95 month-to-month, starting with a 7-day free trial. One plan, everything included.
- **Data:** your own bank accounts, connected read-only via Plaid (US institutions)

> Bank connections are read-only. OpenBudget never stores your bank credentials, and your AI assistant **cannot move money** at your bank. What it can write stays inside your own OpenBudget account: transaction categories, notes, categorization rules, and the manual off-bank accounts and transactions you ask it to add (cash, gift-card balances).

---

## Quick connect

Pick your client below, or see the full step-by-step guides at **[openbudget.sh/connect-ai](https://openbudget.sh/connect-ai)**.

### Claude (claude.ai / Claude Desktop)

1. **Customize** → **Connectors** → **Add custom connector**
2. Name it `openbudget.sh`, paste `https://api.openbudget.sh/mcp`
3. Click **Connect** and sign in with the account you used to link your banks

Full guide: [openbudget.sh/connect-ai/claude](https://openbudget.sh/connect-ai/claude)

### Claude Code

```bash
claude mcp add --transport http openbudget https://api.openbudget.sh/mcp
```

Add `--scope user` to make it available in every project. Then run `/mcp` inside a session, select **openbudget**, and choose **Authenticate**.

Full guide: [openbudget.sh/connect-ai/claude-code](https://openbudget.sh/connect-ai/claude-code)

### ChatGPT (Plus / Team / Enterprise)

1. **Settings** → **Apps** → **Advanced settings** → enable **Developer mode**
2. **Create app**, name it `OpenBudget`, paste the URL, select **OAuth**
3. Sign in and authorize access

Full guide: [openbudget.sh/connect-ai/chatgpt](https://openbudget.sh/connect-ai/chatgpt)

### Cursor

Add to `~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (project):

```json
{
  "mcpServers": {
    "openbudget": {
      "url": "https://api.openbudget.sh/mcp"
    }
  }
}
```

Then enable the server under **Cursor Settings → Tools & MCP → Connect**.

Full guide: [openbudget.sh/connect-ai/cursor](https://openbudget.sh/connect-ai/cursor)

### Codex / Codex CLI

```bash
codex mcp add openbudget --url https://api.openbudget.sh/mcp
codex mcp login openbudget
```

Or add to `~/.codex/config.toml`:

```toml
[mcp_servers.openbudget]
url = "https://api.openbudget.sh/mcp"
```

Full guides: [codex](https://openbudget.sh/connect-ai/codex) · [codex-cli](https://openbudget.sh/connect-ai/codex-cli)

### Any other MCP client

Add a **remote / custom MCP server** over the **Streamable HTTP** transport, pointing at `https://api.openbudget.sh/mcp`. Most clients also accept the `mcpServers` JSON shape shown in the Cursor section above. The first call opens your browser to sign in and approve access.

Full guide: [openbudget.sh/connect-ai/other](https://openbudget.sh/connect-ai/other)

---

## What you can ask

The server ships with 13 tools, each shown with a live demo at **[openbudget.sh/tools](https://openbudget.sh/tools)**. You rarely call them by name — just ask in natural language and your client picks the right one.

| Tool | What it does |
|------|--------------|
| `list_accounts` | Every linked account with balances, institution, and a normalized net-worth view. |
| `search_transactions` | Search and filter transactions — by text, category, account, date range, or amount. |
| `get_liabilities` | Credit cards, student loans, and mortgages: APRs, due dates, minimum payments, payoff data. |
| `get_liability_history` | How those liabilities changed over time — APR moves, balance trends, YTD interest. |
| `list_available_categories` | The full transaction category taxonomy. |
| `list_categorization_rules` | Your auto-categorization rules, in evaluation order. |
| `update_transaction` | *(write)* Recategorize a transaction, add a note, split it, or hide it from searches. |
| `create_transaction` | *(write)* Log an off-bank purchase — physical cash, or something paid entirely with a gift-card balance. |
| `delete_transaction` | *(write)* Remove a manually-created transaction (bank transactions are hidden with `update_transaction` instead). |
| `upsert_manual_account` | *(write)* Create or update a manual off-bank account — a "Cash" wallet or a gift-card balance that sits alongside your bank accounts. |
| `save_categorization_rule` | *(write)* "Payments to our nanny are childcare" — create a rule once, new transactions follow it. |
| `delete_categorization_rule` | *(write)* Remove a rule. |
| `update_liability` | *(write)* Note or manual balance override on a credit card or loan. |

### Example prompts

- *"How much did I spend on restaurants last month, and how does it compare to the month before?"*
- *"What did the Denver trip in March cost me all-in — flights, hotel, food, rideshares?"*
- *"Find every subscription I'm paying for and total them per month."*
- *"What's my net worth across all accounts right now?"*
- *"What's the APR on each of my credit cards, and when are the next payments due?"*
- *"Venmo payments to Elena are childcare, not transfers — make that a rule."*
- *"I paid the babysitter $60 in cash tonight; log it as childcare."*
- *"Track my $200 Amazon gift card and record the $45 I just spent from it."*

More in [`examples/prompts.md`](examples/prompts.md).

---

## Read-only banking by design

Bank connections go through [Plaid](https://plaid.com) with read-only access — the same infrastructure behind Venmo and Robinhood. OpenBudget never sees or stores your bank credentials. Your AI assistant **cannot move money, initiate transfers, or modify anything at your bank.** Every write it can make stays inside your own OpenBudget account: transaction categories, notes, ignored flags, splits, categorization rules, liability notes, and the manual off-bank accounts and transactions you ask it to add (cash, gift-card balances). Every tool declares MCP annotations (`readOnlyHint`, `destructiveHint`) so clients can show you exactly what each call can do.

## Privacy & terms

OAuth grants your AI client access to your own financial data in OpenBudget — nobody else's. See the [Terms of Service](https://openbudget.sh/terms), [Privacy Policy](https://openbudget.sh/privacy), and [Security](https://openbudget.sh/security) pages.

## License

The configuration snippets and documentation in this repository are [MIT-licensed](LICENSE).

The OpenBudget MCP service they connect to is a hosted service governed by its [Terms of Service](https://openbudget.sh/terms) — your use of the service is subject to those terms, not this license. This repository does not contain the server's source code.

## Links

- Website — [openbudget.sh](https://openbudget.sh)
- The tools, demoed — [openbudget.sh/tools](https://openbudget.sh/tools)
- Connect guides — [openbudget.sh/connect-ai](https://openbudget.sh/connect-ai)
- Pricing — [openbudget.sh/pricing](https://openbudget.sh/pricing)
- Model Context Protocol — [modelcontextprotocol.io](https://modelcontextprotocol.io)
