---
description: Quick portfolio overview — current value, total return, top 5 holdings
---

Give me a quick portfolio overview using Parqet.

Steps:
1. Call `parqet_list_portfolios` to get all portfolio IDs
2. For each portfolio, call `parqet_query_portfolio` with `view=overview`
3. Present a concise summary table:
   - Portfolio name
   - Current value (formatted with currency)
   - Total gain/loss (absolute + %)
   - XIRR (annualized return)
   - Top 3 holdings by current value
4. Show combined totals across all portfolios if multiple exist

Format numbers cleanly. XIRR and TTWROR values from Parqet are already percentages — do not multiply by 100.
Keep output compact — one section per portfolio, totals at bottom.
