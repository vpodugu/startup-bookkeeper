# Dashboard Specification

Generate a React (.jsx) artifact that uses `window.storage` for persistence.

## Layout & Sections

### 1. Alert Banner (top, conditional)
- Show only when there are undismissed alerts
- Color-coded by severity: red for critical, amber for warning, blue for info
- Each alert has a dismiss (X) button
- Clicking an alert navigates to the relevant section (e.g., overdue invoice
  scrolls to the Receivables table)

### 2. Summary Cards (top row, 5 cards)
- Total Income (this period)
- Total Expenses (this period)
- Net Cash Flow (income - expenses, green if positive, red if negative)
- Outstanding Receivables (count + total)
- Outstanding Payables (count + total)

### 3. Expense Breakdown (chart)
- Donut or bar chart by category for the selected time period
- Use recharts library
- Show percentages and absolute values in tooltip

### 4. Budget vs Actual (conditional — only if budgets are set)
- Horizontal bar chart showing budget limit vs actual spend per category
- Color-code: green if under budget, amber if >80%, red if over
- Show percentage label on each bar

### 5. Recurring Subscriptions (section)
- Table of all active recurring rules
- Columns: Vendor, Amount, Frequency, Next Due, Status (active/paused)
- Toggle button to pause/resume each rule
- "Add Recurring" button that opens a mini-form
- Show total monthly recurring spend at the top

### 6. Recent Transactions (table)
- Sortable table showing transactions for the selected period
- Columns: Date, Type (color badge), Category, Vendor/Client, Amount, Currency
  indicator (if non-base), Status (clickable to cycle)
- Delete button per row (with confirmation)
- For foreign currency transactions, show original amount on hover/click

### 7. Payables & Receivables (two side-by-side mini-tables)
- What you owe (payables) vs what's owed to you (receivables)
- Highlight overdue items in red with "X days overdue" badge
- Show days remaining for non-overdue items
- "Mark as Paid" quick-action button per row

### 8. Time Period Selector
- Toggle between: This Month, Last Month, This Quarter, This Year, All Time
- Dropdown or pill-style buttons

### 9. Quick Actions Bar
- "Add Transaction" button → opens inline form with fields: type, amount,
  currency (dropdown of common currencies), category (dropdown), vendor/client,
  description, date
- "Generate P&L" button → triggers P&L generation for current period
- "Export CSV" button → downloads all transactions as CSV

## Storage Keys

Same as main SKILL.md. On mount:
1. Load all storage keys in parallel
2. Process recurring rules (auto-log any overdue ones)
3. Generate alerts from current data state
4. Render dashboard

## Recurring Auto-Processing on Dashboard Load

When the dashboard mounts:
1. Load `bk:recurring` rules
2. For each active rule where `next_due <= today`:
   a. Create a new transaction with `source: "recurring_auto"` and
      `recurring_id` pointing to the rule
   b. Advance `next_due` to the next cycle date
   c. Save the transaction and updated rule
3. If any transactions were auto-created, show a brief notification:
   "Auto-logged 2 recurring charges: Notion ($29), GitHub ($149)"

## Alert Generation on Dashboard Load

When the dashboard mounts (after recurring processing):
1. Scan receivables/payables for overdue items → generate critical alerts
2. Compare category spend to budgets → generate warning/critical alerts
3. Check recurring rules due in next 3 days → generate info alerts
4. Merge with existing alerts in `bk:alerts`, deduplicating by `related_id`
5. Remove alerts whose conditions no longer apply (e.g., invoice was paid)

## Design Guidelines

- Clean, professional aesthetic — "founder-friendly Stripe dashboard"
- Light mode default, respect system preference if detectable
- Use Tailwind utility classes for layout
- Use recharts for all charts
- Use lucide-react for icons
- Color palette:
  - Income/positive: emerald-500 (#10b981)
  - Expenses/negative: red-500 (#ef4444)
  - Neutral/info: sky-500 (#0ea5e9)
  - Warnings: amber-500 (#f59e0b)
  - Backgrounds: gray-50 (#f9fafb)
  - Cards: white with gray-100 border
  - Text: gray-900 primary, gray-400 secondary
- Typography: DM Sans (import from Google Fonts) for a modern, clean look
- Responsive — must work on mobile (stack cards vertically, horizontal scroll
  on tables)
- Smooth transitions on state changes (200ms ease)
- Currency formatting: use Intl.NumberFormat with the base currency

## Error Handling

- All storage operations must be wrapped in try-catch
- Show loading spinner while data loads
- If storage is empty (first use), show a friendly empty state with:
  - Illustration or icon
  - "Welcome to Bookkeeper" heading
  - Brief feature list
  - "Add your first transaction" call-to-action button
- Handle malformed data gracefully — log errors to console, don't crash
- If a recurring rule references a deleted or invalid category, flag it
  rather than crashing

## CSV Export Format

When the user clicks Export:
```
Date,Type,Category,Vendor/Client,Description,Amount,Currency,Original Amount,Original Currency,Status,Invoice #,Source
2026-03-21,expense,SaaS & Software,GitHub,Annual Team plan,149.00,USD,,,paid,,manual
2026-03-21,receivable,Consulting & Services,DataVista Corp,Consulting engagement,8500.00,USD,,,unpaid,#2045,manual
```

Include all transactions (not just filtered period) unless the user specifies otherwise.
