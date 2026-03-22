# Changelog

All notable changes to this project will be documented in this file.

## [1.0.0] - 2026-03-21

### Added
- Core conversational expense tracking with smart categorization
- Batch transaction logging (multiple transactions in one message)
- Recurring transactions (monthly, weekly, quarterly, yearly frequencies)
- Receipt and invoice image upload with OCR extraction
- Multi-currency support with original/converted amount tracking
- Interactive React dashboard with persistent storage
  - Summary cards (income, expenses, cash flow, receivables, payables)
  - Expense breakdown donut chart (recharts)
  - Budget vs. actual horizontal bar chart
  - Recurring subscriptions panel with pause/resume
  - Sortable transaction table with inline status editing
  - Payables & receivables with overdue highlighting
  - Time period selector (month, quarter, year, all time)
  - Quick-add transaction form
  - CSV export
- Monthly P&L report generation
- Proactive alerts system (overdue invoices, budget warnings, upcoming charges)
- Separate income categories for revenue/receivable transactions
- Budget setting and tracking per expense category
- Dashboard reference spec in `references/dashboard-spec.md`
