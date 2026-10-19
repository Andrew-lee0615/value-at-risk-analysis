# Value at Risk Analysis: Semiconductor Portfolio

I measured 1-day Value at Risk (VaR) for an equal-weight portfolio of 7 semiconductor stocks using Historical, Parametric, and Monte Carlo (normal and Student-t) methods, then backtested them with a rolling window and the Kupiec test. Only Historical VaR passed at both 95% and 99%, showing that no single model fits every confidence level.

## Key Results

**1-day VaR (full 5-year sample, % of portfolio value)**

| Method | 95% VaR | 99% VaR |
|---|---|---|
| Historical | 4.23% | 6.93% |
| Parametric (normal) | 4.29% | 6.12% |
| Monte Carlo (normal) | 4.22% | 6.01% |
| Monte Carlo (Student-t, df=4) | 3.80% | 6.90% |
| *SPY (Historical, benchmark)* | *1.66%* | *2.97%* |

**Backtest (rolling 250-day window, 1,004 test days, Kupiec test at 5% significance)**

| Model | 95% breaches (expected 50) | Kupiec p-value | 99% breaches (expected 10) | Kupiec p-value |
|---|---|---|---|---|
| Historical | 62 | 0.099 ✅ Pass | 14 | 0.236 ✅ Pass |
| Parametric (normal) | 64 | 0.055 ✅ Pass | 18 | 0.023 ❌ Reject |
| Student-t (df=4) | 79 | 0.000 ❌ Reject | 14 | 0.236 ✅ Pass |

## Key Findings

- **Diversification:** The portfolio's 99% VaR was 20% lower than the average single stock, but still 2.3x SPY, because semiconductor stocks are highly correlated.
- **Fat tails:** Parametric VaR matched Historical at 95% (4.29% vs 4.23%) but underestimated 99% VaR by 12% (6.12% vs 6.93%). A Student-t model (df=4) fixed the 99% tail (6.90%).
- **Backtesting:** Normal VaR failed the Kupiec test at 99% (18 breaches vs 10 expected), and Student-t failed at 95% (79 vs 50). No single model fits every confidence level.

## Notebooks

| Notebook | What it does |
|---|---|
| `01_returns.ipynb` | Download 5 years of prices and compute daily log returns |
| `02_historical_var.ipynb` | Calculate Historical VaR for each stock, the portfolio, and SPY |
| `03_parametric_var.ipynb` | Calculate Parametric VaR, compare with Historical, and test for fat tails |
| `04_monte_carlo_var.ipynb` | Simulate 10,000 returns with normal and Student-t distributions to calculate Monte Carlo VaR |
| `05_backtesting.ipynb` | Backtest each model with a rolling window, compare actual vs expected breaches, and run the Kupiec test |

## Data & Assumptions

- **Stocks:** NVDA, INTC, AMD, MU, MRVL, WDC, AVGO (equal weight). Benchmark: SPY.
- **Data:** 5 years of daily adjusted close prices from Yahoo Finance (`yfinance`), downloaded October 2026.
- **Returns:** Daily log returns. The portfolio return is the simple average of the 7 stocks' log returns (a close approximation of a daily-rebalanced equal-weight portfolio).
- **VaR:** 1-day horizon, reported as a positive % loss.
- **Backtest:** Each day's VaR uses only the previous 250 trading days (no look-ahead bias).

## Tools

Python (pandas, NumPy, SciPy, Matplotlib), yfinance, Google Colab

## Next Steps

- Add Expected Shortfall (ES) and maximum drawdown
- Add GARCH(1,1) VaR so volatility reacts to recent market conditions
