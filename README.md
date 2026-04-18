# Personal Finance Dashboard

A fully client-side, single-file interactive financial dashboard built with vanilla HTML, CSS, and JavaScript. Upload your General Ledger export and get an instant, comprehensive view of your finances — no backend, no database, no data leaves your device.

**Live:** https://personal-finance-dashboard-one-red.vercel.app  
**GitHub:** https://github.com/n8njayonrender/personal-finance-dashboard

---

## Features

### Upload & Data Ingestion
- Accepts `.xlsx`, `.xls`, and `.csv` General Ledger exports
- Drag-and-drop or click-to-browse file upload
- Dates parsed in `dd/mm/yyyy` format (with fallbacks for ISO and serial formats)
- Handles Debit/Credit columns or a single signed Amount column
- All processing runs in-browser — data never leaves your device

### Account Mapping
- Auto-classifies each GL account into: **Income, Expense, Asset, Liability, Equity, Ignore**
- Uses keyword pattern matching against account names
- Click any account card to cycle through types before building the dashboard
- Session persisted in `localStorage` so data survives page refreshes

---

## Dashboard Sections

### KPI Scorecards (Row 1 — Overview)
| KPI | Description |
|-----|-------------|
| Total Balance | All-time net position |
| Total Income | Sum of income for selected period |
| Total Expenses | Sum of expenses for selected period |
| Net Savings | Income minus Expenses |

Each card shows a **Year-over-Year % change badge** (▲/▼) vs the equivalent prior period.

### KPI Scorecards (Row 2 — Financial Health)
| KPI | Description |
|-----|-------------|
| Net Profit Margin | Net / Income × 100 |
| Expense Ratio | Expenses / Income × 100 |
| Avg Monthly Spend | Average monthly expense across all months |
| Avg Monthly Income | Average monthly income across all months |

### Transactions Overview Chart
- Line chart: Earnings, Spending, and Net over time
- Tab modes: All / Earnings / Spending / Net
- Responds to Year Filter and Time Filter

### Year Filter
- Dynamically generated buttons for every year present in your data
- Selecting a year pins all charts and KPIs to that year
- "All" reverts to the full dataset

### Time Filter (Top Bar)
- Week / Month / Year / All
- Works independently of the Year Filter

---

### Year-over-Year Comparison
- 4 summary pills: Current Year Income, Prior Year Income, Current Year Spending, Prior Year Spending — each with % change
- **Income Comparison** bar chart: monthly side-by-side, current year (green) vs prior year (muted)
- **Spending Comparison** bar chart: monthly side-by-side, current year (orange) vs prior year (muted)
- Fully dynamic — updates when any year filter is changed
- Only shows months that have actual data (no empty trailing bars)

---

### Top Expense Categories & Income Sources
- Horizontal bar charts ranked by total value
- Top 8 expense categories, top 6 income sources
- Color-coded bars per rank

### Savings Opportunities
- Automatically identifies your **top 5 highest-spend categories**
- Each entry shows:
  - Category name + share of total spend (%)
  - Tailored, actionable savings tip
  - **Current Spend** (red) → **Suggested Cut at realistic %** (amber) → **Target** (green)
- Header badge shows total potential savings across all 5 categories
- Savings % benchmarks are category-specific (e.g. subscriptions 30%, gym 40%, utilities 15%)

---

### Balance Sheet
- Three cards: **Total Assets** (blue), **Total Liabilities** (amber), **Total Equity** (purple)
- Accounting-correct balances: Assets use debit-normal, Liabilities/Equity use credit-normal
- Each card lists all accounts with % share bars
- "Show all" toggle for accounts beyond top 5
- **Financial Ratios:** Liquidity Ratio, Debt Ratio, Debt-to-Equity, Net Worth

#### Balance Sheet Account Drilldown
Click any account row to open a slide-in drawer with:
- Balance, Total Debits, Total Credits, Entry count
- **Monthly Activity bar chart** — debit (red) vs credit (green) per month
- **Full transaction history table** — Date | Debit | Credit | Running Balance (newest first, cumulative)
- Press `Esc` or click ✕ to close

---

### Outlier Detection
- Flags statistically unusual transactions using **IQR × 1.5** per account
- Listed in a dedicated table with Amount, Normal Threshold, and multiplier
- Outlier rows highlighted in the Transaction Ledger
- Badge count shown in sidebar

### Transaction Ledger
- Full paginated table (10 rows/page) of all Income and Expense transactions
- Sortable by Account, Date, Amount
- Searchable via top bar or sidebar search (synced)
- Filter pills: All / Earnings / Spending
- Outlier rows flagged with a ⚠ OUTLIER badge
- **CSV Export** button

---

## Tech Stack

| Component | Library/Version |
|-----------|----------------|
| Charts | Chart.js 4.4.1 |
| Excel/CSV Parsing | SheetJS (xlsx) 0.18.5 |
| Framework | None — Vanilla HTML/CSS/JS |
| Hosting | Vercel |

---

## File Structure

```
PersonalAdmin/
├── index.html              # Entry point served by Vercel (copy of dashboard)
├── Financial_Dashboard.html # Source dashboard file
├── README.md               # This file
├── GL_12042026.xlsx        # Sample GL export (Apr 12)
├── GL_18042026.xlsx        # Sample GL export (Apr 18)
├── DEPLOY_GUIDE.md         # Deployment notes
├── claude.md               # Project instructions for Claude
└── skills.md               # Claude skills reference
```

---

## Deployment

### Vercel (Live)
Deployed via Vercel CLI directly from local. The `index.html` file is the root entry point.

```bash
vercel --yes --prod
```

**Production URL:** https://personal-finance-dashboard-one-red.vercel.app

### GitHub
```
https://github.com/n8njayonrender/personal-finance-dashboard
```

To push updates:
```bash
git add index.html Financial_Dashboard.html
git commit -m "your message"
git push
vercel --yes --prod
```

---

## Data Format

The dashboard expects a GL export with at minimum:

| Column | Required | Notes |
|--------|----------|-------|
| `Date` | Yes | `dd/mm/yyyy` preferred |
| `Account` / `Description` / `Payee` | Yes | Account name |
| `Debit` + `Credit` | One pair OR | Separate debit/credit columns |
| `Amount` | ...or this | Single signed amount column |
| `Type` | Optional | Helps with debit/credit detection |

Currency symbols (`$`, `,`) are stripped automatically. Missing or unparseable rows are skipped gracefully.

---

## Account Classification Keywords

Auto-detection uses regex pattern matching:

| Type | Keywords |
|------|----------|
| Income | income, revenue, salary, wages, dividend, sales, commission, bonus... |
| Expense | expense, fee, subscription, rent, food, travel, tax, insurance, utilities... |
| Asset | cash, bank, receivable, prepaid, investment, property, equipment, vehicle... |
| Liability | payable, loan, mortgage, credit card, overdraft, borrowing... |
| Equity | equity, retained, capital, reserve, surplus, deficit... |

Unmatched accounts default to **Ignore** and are excluded from all calculations.
