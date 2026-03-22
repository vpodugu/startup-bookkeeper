# 📒 Startup Bookkeeper

**A conversational bookkeeping skill for Claude AI — built for founders who'd rather talk to their books than wrestle with spreadsheets.**

Track expenses, income, invoices, and subscriptions through natural conversation. Get dashboards, P&L reports, and overdue alerts — all without a QuickBooks subscription.

---

## Why This Exists

Early-stage startups and small businesses need to track their money, but most don't need (or can't justify) a $30+/month accounting platform. What they need is:

- A quick way to log expenses as they happen
- Visibility into where the money's going
- Alerts when invoices are overdue or budgets are blown
- A simple P&L to share with co-founders or advisors

This Claude skill does all of that through conversation. Say "paid $149 for GitHub" and it's logged, categorized, and persisted. Say "show me my dashboard" and you get an interactive financial overview. No forms, no setup wizards, no subscriptions.

---

## Features

### 💬 Conversational Expense Tracking
Log transactions in natural language — single or batch.
```
You: "Paid $149 for GitHub, $29 for Notion, and $200 for AWS"
Claude: "Logged 3 expenses:
  1. $149.00 → SaaS & Software · GitHub
  2. $29.00 → SaaS & Software · Notion
  3. $200.00 → SaaS & Software · AWS
  All dated today. Adjust anything?"
```

### 🔄 Recurring Transactions
Set up subscriptions once — they auto-log each cycle.
```
You: "I pay $29/month for Notion on the 15th"
Claude: "Set up recurring expense: $29.00/month → SaaS & Software · Notion
  Next charge: April 15. Auto-log enabled."
```

### 📸 Receipt & Invoice OCR
Upload a photo of a receipt or PDF invoice — Claude reads it and logs the transaction.
```
You: [uploads receipt image]
Claude: "I read this receipt from Staples dated March 15:
  Office supplies: $47.82 | Tax: $3.83 | Total: $51.65
  Category: Office & Equipment. Log this?"
```

### 🌍 Multi-Currency Support
Operating internationally? Log in any currency.
```
You: "Paid ¥15,000 for the Tokyo hotel"
Claude: "Got it — ¥15,000 JPY. What's the approximate USD equivalent?"
```

### 📊 Interactive Dashboard
A full React dashboard with:
- Summary cards (income, expenses, net cash flow, receivables, payables)
- Expense breakdown by category (donut chart)
- Budget vs. actual comparison
- Recurring subscriptions overview
- Sortable transaction table
- Payables & receivables with overdue highlighting
- Time period selector (month, quarter, year, all time)
- Quick-add form and CSV export

### 📋 Monthly P&L Reports
Generate a clean profit & loss statement on demand.
```
You: "Generate my P&L for March"
Claude: [generates structured P&L with revenue, expenses, net income, and notes]
```

### 🔔 Proactive Alerts
Claude warns you about:
- Overdue invoices (critical)
- Budget overruns and warnings at 80% (warning)
- Upcoming recurring charges in the next 3 days (info)

---

## Installation

### Claude.ai (Skills feature)
1. Download `startup-bookkeeper.skill` from [Releases](../../releases)
2. In Claude.ai, go to **Settings → Skills**
3. Upload the `.skill` file
4. Start a conversation: "Log an expense: $45 for AWS"

### Claude Code
```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/startup-bookkeeper.git

# Copy to your skills directory
cp -r startup-bookkeeper ~/.claude/skills/

# Or for project-level
cp -r startup-bookkeeper .claude/skills/
```

### Manual Installation
Copy the `SKILL.md` and `references/` folder into your Claude skills directory.

---

## How It Works

### Data Persistence
All data is stored in Claude's artifact `window.storage` API, which persists across sessions. Storage keys:

| Key | Description |
|---|---|
| `bk:transactions` | All logged transactions |
| `bk:budgets` | Monthly budget targets by category |
| `bk:recurring` | Recurring transaction rules |
| `bk:alerts` | Active and dismissed alerts |
| `bk:settings` | Base currency, company name, preferences |
| `bk:categories` | Custom categories (beyond defaults) |

### Categories

**Expenses:** SaaS & Software, Payroll & Contractors, Marketing & Ads, Travel & Transport, Office & Equipment, Legal & Professional, Meals & Entertainment, Insurance, Taxes & Fees, Utilities & Telecom, Miscellaneous

**Income:** Product Revenue, Consulting & Services, Grants & Awards, Interest & Investments, Refunds, Other Income

Custom categories are created automatically when needed.

### Transaction Types
- **expense** — money going out (paid)
- **income** — money coming in (received)
- **payable** — money you owe (bill due)
- **receivable** — money owed to you (invoice sent)

---

## Examples

### Logging expenses
```
"Paid $120 for domain renewal"
"Spent $45.99 on team lunch at Chipotle"
"AWS bill came in at $847 for February"
```

### Managing invoices
```
"Sent invoice #2045 to Acme Corp for $8,500, due in 30 days"
"Mark the Acme invoice as paid"
"Any overdue invoices?"
```

### Recurring subscriptions
```
"I pay $29/month for Notion on the 15th"
"Show my recurring subscriptions"
"Cancel the Figma subscription"
"Notion went up to $32/month"
```

### Budgets and reporting
```
"Set my SaaS budget to $500/month"
"How much did we spend on marketing this quarter?"
"Generate my P&L for last month"
"Show me my dashboard"
```

### Multi-currency
```
"Paid €350 for conference registration in Berlin"
"Paid £200 for UK hosting"
```

---

## What This Is Not

This skill is a **lightweight tracking tool**, not accounting software. It intentionally does not:

- ❌ Give tax advice or filing guidance
- ❌ Do double-entry / GAAP-compliant accounting
- ❌ Process payroll (withholdings, benefits, pay stubs)
- ❌ Perform live currency conversion (asks you to confirm rates)
- ❌ Generate auditable financial statements

For those needs, consult a CPA or use dedicated accounting software.

---

## Skill Structure

```
startup-bookkeeper/
├── SKILL.md                        # Main skill instructions (439 lines)
└── references/
    └── dashboard-spec.md           # Dashboard artifact specification (134 lines)
```

---

## Contributing

Contributions welcome! Some ideas:

- **More expense categories** for specific industries (healthcare, manufacturing, etc.)
- **Integration patterns** with bank CSV formats (Chase, Bank of America, Mercury, etc.)
- **Localization** — adapt categories and P&L format for non-US markets
- **Budget templates** — pre-built budget profiles for common startup stages (pre-seed, seed, Series A)

Please open an issue first to discuss significant changes.

---

## License

MIT License — see [LICENSE](LICENSE) for details.

---

## Acknowledgments

Built with the [Claude Skill Creator](https://github.com/anthropics/skills) framework. Inspired by the real pain of tracking startup expenses in scattered spreadsheets.

---

**Built by [Q3 Learners LLC](https://q3learners.com)** · Made with Claude
