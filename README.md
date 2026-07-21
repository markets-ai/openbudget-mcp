# OpenBudget MCP Server

**Your bank accounts, spending, debts, and investments are available to your AI assistant.**

OpenBudget is a hosted [Model Context Protocol](https://modelcontextprotocol.io) (MCP) server that gives Claude, ChatGPT, Cursor, Codex, and other MCP clients access to your financial data. That includes transactions, balances, net worth, credit cards, loans, mortgages, investment holdings, and investment activity synced from US institutions through Plaid. You can also add physical cash, gift-card balances, real estate, private-company interests, and other assets you track yourself.

You don't run anything. The server is hosted on OpenBudget's infrastructure. You add one URL to your client, sign in once, and start asking.

```
https://api.openbudget.sh/mcp
```

- **Transport:** Streamable HTTP (remote MCP server)
- **Auth:** OAuth 2.0; sign in with your OpenBudget account
- **Plans:** [Base](https://openbudget.sh/pricing) is $60/year or $9.95 month-to-month. Investor is $99/year or $12.95 month-to-month. Both start with a 7-day free trial.
- **Data:** your own bank, brokerage, and retirement accounts connected through Plaid, plus assets and activity you add yourself

> Plaid connections provide read-only financial data. OpenBudget never stores your financial-institution credentials, and your AI assistant **cannot move money or place trades**. Every write stays inside your OpenBudget account: categories, notes, rules, manual accounts, custom holdings, and custom activity.

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

The server ships with 22 tools, each shown with a live demo at **[openbudget.sh/tools](https://openbudget.sh/tools)**. You rarely call them by name. Ask in natural language and your client picks the right one.

### Base tools

| Tool | What it does |
|------|--------------|
| `list_accounts` | Every linked account with balances, institution, and a normalized net-worth view. |
| `search_transactions` | Search and filter transactions by text, category, account, date range, or amount, with settlement links and merchant location details when Plaid supplies them. |
| `get_liabilities` | Credit cards, student loans, and mortgages: APRs, due dates, minimum payments, payoff data. |
| `get_liability_history` | How those liabilities changed over time, including APR moves, balance trends, and YTD interest. |
| `list_available_categories` | The full transaction category taxonomy. |
| `list_categorization_rules` | Your auto-categorization rules, in evaluation order. |
| `update_transaction` | *(write)* Recategorize a transaction, add a note, split it, or hide it from searches. |
| `create_transaction` | *(write)* Log an off-bank purchase made with physical cash, a gift card, or store credit. |
| `delete_transaction` | *(write)* Remove a manually-created transaction (bank transactions are hidden with `update_transaction` instead). |
| `upsert_manual_account` | *(write)* Create or update a manual off-bank account such as a cash wallet or gift-card balance. |
| `save_categorization_rule` | *(write)* Create a rule once, such as "payments to our nanny are childcare," and new transactions follow it. |
| `delete_categorization_rule` | *(write)* Remove a rule. |
| `update_liability` | *(write)* Note or manual balance override on a credit card or loan. |

### Investor tools

Investor includes every Base tool and adds the tools below.

| Tool | What it does |
|------|--------------|
| `search_investment_holdings` | Filter connected and custom positions by account, ticker, name, category, source, or closed status, then sort by value, quantity, name, or ticker. Returns the position data your AI needs to calculate allocation, gains, and concentration. |
| `search_investment_transactions` | Search buys, sells, dividends, interest, contributions, withdrawals, fees, taxes, property activity, and adjustments. |
| `list_available_investment_categories` | Return the separate category taxonomies for holdings and investment activity. |
| `update_investment_holding` | *(write)* Categorize a holding, change its display fields, or manage its notes. Custom holdings also support value, quantity, price, cost basis, ticker, currency, and status changes. |
| `update_investment_transaction` | *(write)* Categorize investment activity, add a note, or ignore it. Custom activity also supports amount, date, quantity, price, fees, ticker, and currency changes. |
| `create_investment_holding` | *(write)* Add real estate, private-company interests, collectibles, offline positions, or other custom assets. |
| `delete_investment_holding` | *(write)* Delete a custom holding. Connected holdings remain synced from the institution. |
| `create_investment_transaction` | *(write)* Add property purchases, distributions, private contributions, fees, valuation adjustments, and other custom investment activity. |
| `delete_investment_transaction` | *(write)* Delete custom investment activity. Connected activity can be ignored instead. |

Transaction results can include `pendingTransactionId`, which links a posted transaction to its earlier pending record, and a structured `location` with address, city, region, postal code, store number, latitude, and longitude. These fields make receipt reconciliation reliable when a tip or pre-authorization changes the settled amount.

### Example prompts

- *"How much did I spend on restaurants last month, and how does it compare to the month before?"*
- *"What did the Denver trip in March cost me all-in: flights, hotel, food, and rideshares?"*
- *"Find every subscription I'm paying for and total them per month."*
- *"What's my net worth across all accounts right now?"*
- *"What's the APR on each of my credit cards, and when are the next payments due?"*
- *"Categorize Venmo payments to Elena as childcare and make that a rule."*
- *"I paid the babysitter $60 in cash tonight; log it as childcare."*
- *"Track my $200 Amazon gift card and record the $45 I just spent from it."*
- *"What's my net worth across bank accounts, cards, loans, brokerage and retirement accounts, and property?"*
- *"Over the last three months, how much did I spend, save, and invest, and what changed month to month?"*
- *"Add my rental property and record this month's distribution."*

More in [`examples/prompts.md`](examples/prompts.md).

---

## Read-only financial connections

Bank, brokerage, and retirement connections go through [Plaid](https://plaid.com) with read-only data access. OpenBudget never sees or stores your financial-institution credentials. Your AI assistant **cannot move money, initiate transfers, place trades, or modify anything at your institution.** Every write stays inside your OpenBudget account: transaction and investment categories, notes, ignored flags, splits, rules, liability overrides, manual accounts, custom holdings, and custom activity. Every tool declares MCP annotations (`readOnlyHint`, `destructiveHint`) so clients can show what each call can do.

## Privacy & terms

OAuth grants your AI client access to your own financial data in OpenBudget. See the [Terms of Service](https://openbudget.sh/terms), [Privacy Policy](https://openbudget.sh/privacy), and [Security](https://openbudget.sh/security) pages.

## License

The configuration snippets and documentation in this repository are [MIT-licensed](LICENSE).

The OpenBudget MCP service is hosted and governed by its [Terms of Service](https://openbudget.sh/terms). The MIT license covers the configuration snippets and documentation in this repository. This repository does not contain the server's source code.

## Links

- Website: [openbudget.sh](https://openbudget.sh)
- Tool demos: [openbudget.sh/tools](https://openbudget.sh/tools)
- Connect guides: [openbudget.sh/connect-ai](https://openbudget.sh/connect-ai)
- Pricing: [openbudget.sh/pricing](https://openbudget.sh/pricing)
- Model Context Protocol: [modelcontextprotocol.io](https://modelcontextprotocol.io)
