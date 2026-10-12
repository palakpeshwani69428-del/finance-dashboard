# MONETA — Personal Finance Dashboard

A single-file, self-contained personal finance dashboard that tracks **expenses, subscriptions, savings goals and budgets** in one interface. No build step, no dependencies, no backend — open the HTML file and it runs.

---

## How to open it

**Option 1 — direct (nothing to do):** the page is already live —
`https://static.teamily.ai/sites/c1fccc75-b634-4f87-ab65-fc3b6bc50205/webpages/finance-dashboard/index.html`

**Option 2 — download and open locally:**
1. Download `index.html`.
2. Double-click it, or drag it into any modern browser (Chrome, Safari, Firefox, Edge).
3. That's it — no server, no `npm install`, no build.

**Option 3 — serve it (optional):**
```bash
python3 -m http.server 8000
# then visit http://localhost:8000/index.html
```

Works fully offline — there are **no external requests** (no CDNs, no fonts, no analytics).

---

## What's inside

| # | Section | What it does |
|---|---------|--------------|
| 1 | **Overview** | Total balance, this month's income, this month's spending, and net savings rate (animated progress ring). A 30-day balance sparkline and a cash-flow split bar showing how income divided between spending and savings. |
| 2 | **Expenses** | Interactive SVG donut chart of an 8-category spending breakdown with a hover-linked legend. Month-over-month comparison bars per category, plus a filterable, searchable transaction list (filter by category, search by merchant). |
| 3 | **Subscriptions** | 11 recurring services with cost, billing cycle, monthly-equivalent, next renewal date and last-used date. Summary tiles for monthly/annual totals. Automatic flags for **renewing soon**, **likely unused** (30+ days idle), and **duplicate** services in the same category. |
| 4 | **Savings Goals** | Four goals (emergency fund, travel, laptop, down payment) with target, saved amount, percentage complete, amount remaining and a **projected completion date** derived from the monthly contribution. |
| 5 | **Budgets** | Per-category monthly budget vs. actual spend. Progress bars run **mint below 80%**, **amber at 80–99%** and **red at 100%+**, each with a plain-language "X left" / "X over budget" line and a days-remaining counter. |
| 6 | **Insights** | Eight generated plain-language observations: biggest spending category, subscription load as a share of income, budget overruns, savings pace, duplicate subscriptions, unused services, biggest month-over-month mover, and the overall month trend. |

### Live simulation

The dashboard feels active because it is running a small in-browser simulation loop:

- Every ~3.4 seconds a new plausible transaction is booked against a rotating category, at a realistic merchant and amount.
- The new row flashes into the transaction table and the balance/spend KPIs pulse.
- The donut, month-over-month bars, budgets and all insights recompute immediately.
- A **Pause / Resume** button in the top bar halts or restarts the feed, and a sync indicator shows how many seconds ago the last event landed.

Everything is generated **client-side** from a seeded PRNG — the figures are illustrative, not connected to any real account.

---

## Data model

All state lives in one place in the `<script>` — no storage, no network:

```js
CATS     // 8 spending categories: id, display name, chart color
BUDGETS  // monthly budget per category id
MOM      // month-over-month multiplier per category (current vs. previous)
SUBS     // subscriptions: name, category, cost, cycle, renewal day, last used
GOALS    // goals: name, icon, target, saved so far, monthly contribution
state    // live runtime: balance, income, transactions[], filters, live flag
```

Derived values (spend per category, previous-month totals, subscription monthly/annual totals, renewal countdowns, budget status, insights) are all computed from the above at render time, so changing a number in `CATS`, `BUDGETS`, `SUBS`, `GOALS` or `INCOME_BASE` immediately flows through every chart and every insight.

**To point it at your own data:** replace the `buildData()` function with a fetch of your own transactions and map them to `{date, merchant, cat, amount, method}` — the rest of the render pipeline needs no changes.

---

## Design

Dark slate UI with a mint accent, tabular-figure monospace for all numerics, subtle glow accents and staggered entrance animations. The eight chart colors are fixed and distinguishable and reused consistently across the donut, legend, month-over-month bars and budget rows, so a category reads the same everywhere. Fully responsive — a four-column KPI grid collapses to two then one, and the two-column panels stack on smaller screens.

---

## Tech

Plain HTML + CSS + vanilla JavaScript in a single file. Charts are hand-built SVG (donut via `stroke-dasharray` arcs, sparkline via a scaled polyline path) and canvas-free — no charting library, no framework, no build tooling.
