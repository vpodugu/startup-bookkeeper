# Example Conversations

Real-world usage patterns for the Startup Bookkeeper skill.

---

## Scenario 1: First Day Setup

**You:** I'm starting to track my startup expenses. We're a SaaS company, mostly spending on cloud and tools.

**Claude:** Welcome to Bookkeeper! I can help you track expenses, income, invoices, and subscriptions through conversation. Here's what I can do:

- **Log expenses** — just tell me what you paid for
- **Track invoices** — payables (what you owe) and receivables (what's owed to you)
- **Recurring charges** — set up subscriptions so they auto-log each month
- **Dashboards & P&L** — visualize your spending anytime
- **Alerts** — I'll warn you about overdue invoices and budget overruns

What currency do you primarily operate in? (I'll default to USD if you don't specify.)

**You:** USD is fine. Let me log our monthly subscriptions: AWS $847/month, GitHub $149/year, Notion $29/month, Figma $45/month, Slack $25/month.

**Claude:** Set up 5 recurring charges:

1. **$847.00/month** → SaaS & Software · AWS · Next: April 21
2. **$149.00/year** → SaaS & Software · GitHub · Next: March 21, 2027
3. **$29.00/month** → SaaS & Software · Notion · Next: April 21
4. **$45.00/month** → SaaS & Software · Figma · Next: April 21
5. **$25.00/month** → SaaS & Software · Slack · Next: April 21

Total monthly recurring: ~$958.42 (including GitHub prorated). All auto-log enabled. Want to set a SaaS budget to track against?

---

## Scenario 2: Daily Expense Logging

**You:** Paid $45 for team lunch, $12.99 for a .io domain, and got a $200 Uber receipt from the airport trip last Tuesday.

**Claude:** Logged 3 expenses:

1. **$45.00** → Meals & Entertainment · Team lunch · March 21
2. **$12.99** → SaaS & Software · .io domain registration · March 21
3. **$200.00** → Travel & Transport · Uber airport trip · March 18

Adjust anything?

---

## Scenario 3: Invoice Tracking

**You:** We just sent an invoice to TechCorp for $15,000 for the Q1 consulting project. Net 45.

**Claude:** Logged receivable:

- **$15,000.00** → Consulting & Services · TechCorp
- Due: May 5, 2026 (45 days)
- Status: unpaid

I'll flag it if it goes overdue. Want to add an invoice number?

**You:** Yeah, invoice #3001.

**Claude:** Updated — invoice #3001 attached to the TechCorp receivable.

---

## Scenario 4: Receipt Upload

**You:** [uploads photo of a Staples receipt]

**Claude:** I read this receipt from **Staples** dated March 19, 2026:

- Printer paper (2x): $24.98
- Ink cartridges: $67.49
- USB hub: $19.99
- Tax: $9.02
- **Total: $121.48**
- Payment: Visa ending 4821

Category: Office & Equipment. Log this?

**You:** Yes

**Claude:** Logged: $121.48 → Office & Equipment · Staples · March 19

---

## Scenario 5: Dashboard & Reporting

**You:** Show me where my money went this month.

**Claude:** [generates interactive dashboard artifact]

Here's your March overview:
- **Total expenses:** $1,243.47 across 12 transactions
- **Top category:** SaaS & Software ($958.42 / 77%)
- **Outstanding receivables:** $15,000 (1 invoice — TechCorp, due May 5)
- **No overdue items**

The dashboard is interactive — you can switch time periods and click on categories for details.

---

## Scenario 6: P&L Report

**You:** Generate a P&L for Q1.

**Claude:** [generates P&L report]

```
PROFIT & LOSS STATEMENT
Q1 2026 (January – March)
================================

REVENUE
  Product Revenue          $4,200.00
  Consulting & Services    $0.00 (invoiced: $15,000 pending)
  ─────────────────────────────────
  TOTAL REVENUE            $4,200.00

EXPENSES
  SaaS & Software          $2,875.26
  Travel & Transport       $643.00
  Office & Equipment       $321.48
  Meals & Entertainment    $189.00
  ─────────────────────────────────
  TOTAL EXPENSES           $4,028.74

  ═════════════════════════════════
  NET INCOME               $171.26
  ═════════════════════════════════

NOTES
- Largest expense: SaaS & Software (71% of total)
- Outstanding receivables: $15,000 (1 invoice)
- This is an informational summary, not a GAAP-compliant statement.
```

Want this as a downloadable document?

---

## Scenario 7: Multi-Currency

**You:** Paid ₹45,000 for our India-based contractor this month.

**Claude:** Got it — ₹45,000 INR. What's the approximate USD equivalent? (At recent rates, ₹45,000 ≈ ~$535, but adjust if your bank shows a different amount.)

**You:** Bank charged $540.

**Claude:** Logged: **$540.00** (₹45,000 INR) → Payroll & Contractors · India contractor · March 21. Original amount preserved for your records.

---

## Scenario 8: Alerts

**You:** Any issues I should know about?

**Claude:** Checking your books... 2 items:

🔴 **Invoice #2089 to DataVista ($8,500) is 12 days overdue** — it was due March 9. Want me to help draft a follow-up?

🟡 **SaaS spending is at 87% of your $1,100 budget** — you've spent $958.42 with 10 days left in March.

Everything else looks healthy.
