---
description: Detailed portfolio performance — XIRR, TTWROR, dividends, fees, by time period
---

Show detailed portfolio performance using Parqet. Arguments: $ARGUMENTS (optional: time period like "ytd", "1y", "3y", "max")

Steps:
1. Parse $ARGUMENTS for a time interval preset (ytd/1y/3y/5y/max). Default: ytd
2. Call `parqet_list_portfolios` to get all portfolios
3. For each portfolio, call `parqet_query_portfolio` with:
   - `view=overview`
   - `intervalValue=<parsed interval>`
4. Also call `parqet_get_activity_summary` for dividends and fees in the period
5. Present per portfolio:
   - Period label and date range
   - Current value vs. invested capital
   - XIRR (annualized) and TTWROR (time-weighted)
   - Realized vs. unrealized gains
   - Dividends received
   - Fees and taxes paid
   - Net return after fees
6. Add comparison table if multiple portfolios

XIRR and TTWROR from Parqet are already percentages. Do not multiply by 100.
Flag anything notable: unusually high fees, negative XIRR, large unrealized losses.
