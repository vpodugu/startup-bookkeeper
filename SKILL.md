---
name: startup-bookkeeper
description: >
  Lightweight bookkeeping and expense tracking for startups and small businesses.
  Use whenever the user tracks expenses, logs income, records invoices, manages
  payables/receivables, categorizes transactions, views spending, or wants a
  financial dashboard. Also trigger for: bills, receipts, cash flow, budget
  tracking, P&L reports, vendor payments, client invoices, recurring subscriptions,
  multi-currency transactions, receipt/invoice image uploads, or bank CSV imports.
  Trigger for casual mentions like "I paid for X", "log this expense", "what do
  I owe", "show my spending", "generate my P&L", "any overdue invoices?", or
  "I pay $29/month for Notion". Do NOT trigger for tax filing, depreciation,
  payroll processing, or audit preparation.
---

# Startup Bookkeeper

A conversational bookkeeping skill that lets startup founders and small business
owners track expenses, income, payables, and receivables without needing a paid
subscription to accounting software. Data persists across sessions using artifact
storage.

## Core Principles

1. **Conversational-first**: Users describe transactions naturally ("paid $45 for
   AWS this morning", "sent invoice #1042 to Acme for $5,000"). Extract structured
   data from casual language.
2. **Smart categorization**: Auto-categorize transactions using context clues. If
   uncertain, ask — don't guess wrong.
3. **Persistent storage**: All data lives in `window.storage` inside React artifacts,
   keyed so it survives across sessions.
4. **Dashboard on demand**: Generate a rich, interactive React artifact dashboard
   whenever the user wants to see their financial picture.
5. **Proactive alerts**: Surface overdue invoices, budget overruns, and upcoming
   recurring charges without being asked.
6. **Not an accountant**: This is a tracking tool. Never give tax advice, audit
   opinions, or accounting guidance. If the user asks for that, suggest they
   consult a CPA.

---

## Data Model

### Transaction Object

```json
{
  "id": "txn_<timestamp>_<random4>",
  "date": "YYYY-MM-DD",
  "type": "expense | income | payable | receivable",
  "amount": 120.00,
  "currency": "USD",
  "original_amount": null,
  "original_currency": null,
  "category": "<see categories below>",
  "vendor_or_client": "Amazon Web Services",
  "description": "Monthly EC2 + S3 charges",
  "status": "paid | unpaid | partial | overdue",
  "invoice_number": null,
  "due_date": null,
  "tags": ["infrastructure"],
  "notes": "",
  "recurring_id": null,
  "source": "manual | csv_import | receipt_ocr | recurring_auto"
}
```

When a transaction originates from a foreign currency, store the user-entered
amount in `original_amount` / `original_currency` and the converted amount in
`amount` / `currency` (the user's base currency). This keeps reporting consistent
while preserving the original figures.

### Recurring Rule Object

```json
{
  "id": "rec_<random8>",
  "vendor_or_client": "Notion",
  "amount": 29.00,
  "currency": "USD",
  "category": "SaaS & Software",
  "description": "Monthly subscription",
  "type": "expense",
  "frequency": "monthly | weekly | quarterly | yearly",
  "next_due": "YYYY-MM-DD",
  "day_of_month": 15,
  "auto_log": true,
  "active": true
}
```

### Budget Model

```json
{
  "month": "YYYY-MM",
  "budgets": {
    "SaaS & Software": { "limit": 500, "currency": "USD" },
    "Marketing & Ads": { "limit": 1000, "currency": "USD" }
  }
}
```

### Alert Object

```json
{
  "id": "alert_<random8>",
  "type": "overdue_invoice | budget_exceeded | budget_warning | recurring_upcoming | recurring_failed",
  "severity": "critical | warning | info",
  "message": "Invoice #2045 to DataVista Corp ($8,500) is 5 days overdue",
  "related_id": "txn_xxx or rec_xxx",
  "created_at": "YYYY-MM-DD",
  "dismissed": false
}
```

### Expense Categories

Use these standard categories. If a transaction doesn't fit, create a new one
and inform the user.

| Category | Examples |
|---|---|
| SaaS & Software | AWS, GitHub, Figma, Slack, domain renewals |
| Payroll & Contractors | Salaries, freelancer payments, stipends |
| Marketing & Ads | Google Ads, social media, sponsorships |
| Travel & Transport | Flights, hotels, Uber, gas |
| Office & Equipment | Laptops, monitors, furniture, co-working |
| Legal & Professional | Attorney fees, CPA, trademark filings |
| Meals & Entertainment | Team meals, client dinners |
| Insurance | Business insurance, health insurance |
| Taxes & Fees | State fees, franchise tax, licenses |
| Utilities & Telecom | Internet, phone, electricity |
| Miscellaneous | Anything that doesn't fit above |

### Income Categories

For income and receivable transactions, use these instead of expense categories:

| Category | Examples |
|---|---|
| Product Revenue | SaaS subscriptions, license fees |
| Consulting & Services | Hourly/project billing, retainers |
| Grants & Awards | Government grants, accelerator awards |
| Interest & Investments | Bank interest, investment returns |
| Refunds | Vendor refunds, credit card cashback |
| Other Income | Miscellaneous income |

---

## Interaction Patterns

### 1. Logging a Transaction (conversational)

When the user describes a transaction in natural language:

1. Parse the message to extract: amount, date (default today), type, category,
   vendor/client, description, currency (default to user's base currency)
2. Confirm the parsed transaction back to the user in a brief summary
3. If anything is ambiguous (e.g., "paid John" — is John a contractor or a client?),
   ask one clarifying question
4. If the user mentions a foreign currency ("paid €200 for..."), store original
   currency and ask for or look up the approximate exchange rate
5. Store the transaction

**Batch logging**: When a user logs multiple transactions in one message, parse
all of them and present a numbered summary for confirmation. Example:

- User: "Paid $149 for GitHub, $29 for Notion, and $12 for the .com renewal"
- Claude: "Logged 3 expenses:
  1. **$149.00** → SaaS & Software · GitHub
  2. **$29.00** → SaaS & Software · Notion
  3. **$12.00** → SaaS & Software · Domain renewal
  All dated today. Adjust anything?"

### 2. Logging from CSV / File Upload

When the user uploads a CSV, Excel, or similar file:

1. Read the file using the appropriate tool (bash for CSV, xlsx skill for Excel)
2. Show the user a preview of what was detected: column mapping, number of rows,
   date range
3. Ask user to confirm the mapping (which column is amount, date, description, etc.)
4. Process and categorize each row
5. Show a summary: "Imported 47 transactions totaling $12,340. Here's the category
   breakdown: ..." and generate the dashboard artifact
6. Store all transactions

### 3. Receipt / Invoice Image Upload (OCR)

When the user uploads an image (photo of a receipt, screenshot of an invoice,
or a PDF invoice):

1. Use Claude's vision capability to read the image
2. Extract: vendor name, date, line items (if visible), total amount, tax amount,
   payment method, currency
3. Present the extracted data for confirmation:
   "I read this receipt from **Staples** dated March 15:
   - Office supplies: $47.82
   - Tax: $3.83
   - **Total: $51.65**
   Category: Office & Equipment
   Log this?"
4. If the image is blurry or partially unreadable, tell the user which fields
   couldn't be extracted and ask them to fill in the gaps
5. For multi-page invoices or PDFs, process all visible pages

**Edge cases to handle:**
- Receipts in foreign languages: extract amounts and dates (usually universal
  formats), ask the user to confirm vendor name
- Handwritten receipts: do best-effort OCR, flag low-confidence fields
- Screenshots of digital invoices (Stripe, PayPal, etc.): these are usually
  very clean — extract confidently
- Multiple receipts in one image: process each separately

### 4. Querying Data

The user may ask questions like:
- "What did I spend on SaaS last month?"
- "Show me all unpaid invoices"
- "How much does Acme owe us?"
- "What's my total spend this quarter?"
- "Any overdue invoices?"
- "What are my recurring subscriptions?"

Answer these with a clear text summary first. If the answer involves multiple
items or a broad view, also generate or update the dashboard artifact.

### 5. Updating / Correcting

- "Move that AWS charge to Infrastructure"
- "Mark the Acme invoice as paid"
- "Delete the duplicate entry from March 3"
- "Change the amount on invoice #1042 to $5,500"
- "Cancel the Notion recurring charge"

Confirm destructive changes (deletes). For updates, apply and confirm.

### 6. Dashboard

When the user says "show me my dashboard", "how's my spending", "financial overview",
or similar — generate the React dashboard artifact.
See `references/dashboard-spec.md` for the full dashboard specification.

### 7. Recurring Transactions

Users can set up recurring transactions so subscriptions and regular expenses
don't need to be logged manually each month.

**Setting up a recurring transaction:**
- User: "I pay $29/month for Notion on the 15th"
- Claude: "Set up recurring expense:
  - **$29.00/month** → SaaS & Software · Notion
  - Next charge: April 15, 2026
  - Auto-log: enabled
  I'll automatically log this each month. You can say 'show my recurring' to
  see all subscriptions."

**How recurring works in the dashboard:**
- The dashboard checks `bk:recurring` on load
- Any rules where `next_due <= today` and `auto_log = true` generate a
  transaction automatically, then advance `next_due` to the next cycle
- A "Recurring" section in the dashboard shows all active rules with
  next due dates and a toggle to pause/resume

**Frequency calculations:**
- monthly: same day next month (handle month-end: if day=31 and next month
  has 30 days, use last day of month)
- weekly: +7 days
- quarterly: +3 months
- yearly: +12 months

**Edge cases:**
- If the user says "cancel my Notion subscription", set `active: false` but
  keep the rule for historical reference
- If the user changes the amount ("Notion went up to $32/month"), update the
  rule and note the change date

### 8. Setting Budgets

- "Set my SaaS budget to $500/month"
- "I want to keep marketing under $2,000 this quarter"

Store budget data and reflect it in the dashboard as budget-vs-actual bars.

### 9. Monthly P&L Report

When the user asks for a P&L, profit/loss statement, or monthly financial summary:

1. Pull all transactions for the requested period (default: last completed month)
2. Generate a structured report as a downloadable artifact (markdown or docx):

```
PROFIT & LOSS STATEMENT
[Company Name] — [Month Year]
================================

REVENUE
  Product Revenue          $X,XXX.XX
  Consulting & Services    $X,XXX.XX
  Other Income             $X,XXX.XX
  ─────────────────────────────────
  TOTAL REVENUE            $XX,XXX.XX

EXPENSES
  SaaS & Software          $X,XXX.XX
  Payroll & Contractors     $X,XXX.XX
  Marketing & Ads           $X,XXX.XX
  [... other categories with non-zero amounts]
  ─────────────────────────────────
  TOTAL EXPENSES           $XX,XXX.XX

  ═════════════════════════════════
  NET INCOME               $XX,XXX.XX
  ═════════════════════════════════

NOTES
- Largest expense category: [category] ([%] of total)
- vs. previous month: expenses [up/down] [%]
- Outstanding receivables: $X,XXX ([count] invoices)
- Outstanding payables: $X,XXX ([count] bills)
```

3. Also offer a comparison to the previous month if data exists
4. If the user asks for a specific format (CSV, Excel), generate that instead
5. Footer: "This is an informational summary, not a GAAP-compliant statement."

### 10. Alerts System

The bookkeeper proactively surfaces important financial events. Alerts are
checked and generated whenever the dashboard loads or the user interacts
with the skill.

**Alert triggers:**

| Trigger | Severity | Message |
|---|---|---|
| Invoice past due_date, status != paid | critical | "Invoice {#} to {client} (${amount}) is {N} days overdue" |
| Category spend > budget limit | critical | "{Category} spending exceeded your ${limit} budget by ${over}" |
| Category spend > 80% of budget | warning | "{Category} spending is at {pct}% of your ${limit} budget" |
| Recurring charge due in next 3 days | info | "Upcoming: {vendor} ${amount} due on {date}" |

**How alerts appear:**
- In the dashboard: an alert banner at the top with dismiss buttons
- In conversation: when the user opens any bookkeeping interaction, check for
  critical alerts and mention them proactively. Example: "Heads up — you have
  2 overdue invoices totaling $12,500. Want to review them?"
- Dismissed alerts don't reappear unless the condition recurs

**Overdue auto-detection:**
When loading transactions, scan all payables and receivables. If `due_date < today`
and `status` is still "unpaid" or "partial", automatically update status to
"overdue" and generate an alert.

### 11. Multi-Currency Support

For startups operating internationally or paying for services in foreign currencies.

**Base currency**: Stored in `bk:settings` as `base_currency` (default: "USD").
The user can change this: "Set my base currency to EUR".

**Logging foreign currency transactions:**
- User: "Paid ¥15,000 for the Tokyo hotel"
- Claude: "Got it — ¥15,000 JPY. What's the approximate USD equivalent?
  (Or I can use ~$100 based on typical rates)"
- Store both original and converted amounts

**Exchange rate handling:**
- Claude does NOT do live currency conversion (no API access for rates)
- Ask the user for the converted amount, or suggest an approximation
  based on general knowledge of exchange rates
- Always be transparent: "I'm estimating based on approximate rates — adjust
  if your bank charged a different rate"

**Supported currency symbols for parsing:**
$, €, £, ¥, ₹, ₩, A$, C$, CHF, SEK, NOK, DKK, and 3-letter ISO codes

---

## Storage Keys

Use these keys in `window.storage` (personal, shared=false):

| Key | Value | Description |
|---|---|---|
| `bk:transactions` | JSON array of all transactions | Main data store |
| `bk:budgets` | JSON object of monthly budgets | Budget targets |
| `bk:categories` | JSON array of custom categories | User-added categories |
| `bk:settings` | JSON object | Currency, fiscal year start, company name |
| `bk:recurring` | JSON array of recurring rules | Subscription tracking |
| `bk:alerts` | JSON array of alert objects | Active/dismissed alerts |

Important: Batch related data into single storage keys to minimize API calls.
Load all data on mount, save after changes.

---

## Important Boundaries

- **Not tax advice**: If someone asks about tax categories, deductions, or filings,
  say: "I can track and categorize your transactions, but for tax-specific advice,
  please consult a CPA or tax professional."
- **Not double-entry accounting**: This is single-entry cash-basis tracking. Don't
  try to maintain a chart of accounts, journal entries, or balance sheets.
- **Not payroll processing**: Track payroll as an expense category, but don't
  calculate withholdings, benefits, or generate pay stubs.
- **No live exchange rates**: Claude cannot fetch real-time FX rates. Always ask
  the user to confirm converted amounts or use clearly-stated approximations.
- **Data privacy**: All data stays in the user's personal storage (shared=false).
  Never suggest sharing financial data.
- **P&L is informational**: Always note that generated reports are not
  GAAP-compliant financial statements.

---

## First-Time Experience

If there's no existing data in storage (the user has never used the skill before):

1. Greet them and briefly explain what the bookkeeper can do (tracking, recurring,
   receipt uploads, dashboards, P&L reports, alerts)
2. Ask: "What currency do you primarily operate in?" (default USD if not specified)
3. Ask: "Would you like to start by logging some transactions, uploading a CSV
   of past expenses, setting up your recurring subscriptions, or configuring
   your budget categories?"
4. Guide them through their first few entries to establish the pattern
5. After 3+ transactions, offer to show the dashboard

---

## Dashboard Specification

For the full dashboard layout, design guidelines, and component details,
read `references/dashboard-spec.md`.
