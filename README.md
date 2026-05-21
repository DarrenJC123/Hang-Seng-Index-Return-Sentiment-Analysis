# HSI Sentiment Strategy Backtester

## Overview
This project analyses 747 trading days of Hang Seng Index (HSI) OHLC data paired with social‑media sentiment votes (up‑votes / down‑votes collected before market open). It tests whether lagged sentiment signals can be used to generate profitable trading strategies.

**Key finding:** A contrarian strategy (long when previous day’s net sentiment ≤ -0.15, short when ≥ +0.15) achieves a total return of +78.8% vs. +13.1% for Buy & Hold over the same period.

## Repository Structure
├── HSI.xlsx # Raw data (Date, OHLC, Up votes, Down votes)
├── sentiment_analysis.ipynb # Main Jupyter Notebook with full analysis
├── README.md # This file
└── requirements.txt # Python dependencies

## Data Description
- **Period:** 747 trading days (approx. 3 years)
- **Columns:**
  - `Date` – trading date
  - `Open`, `High`, `Low`, `Close` – HSI daily prices
  - `Up votes`, `Down votes` – social media sentiment votes collected before market open
- **Derived fields:**
  - `Daily Return` = `Close.pct_change()`
  - `Sentiment` = `Up votes - Down votes`
  - `Sentiment Lagged` = `Sentiment.shift(1)` – used to avoid look‑ahead bias

## Strategies Tested

| Strategy | Rule |
|----------|------|
| **Buy & Hold** | Always long (benchmark) |
| **Sentiment Momentum** | Long if Lagged Sentiment > 0, else Short |
| **Threshold (±0.10)** | Long if > +0.10, Short if < -0.10, else Flat |
| **Contrarian (±0.15)** | Short if > +0.15, Long if < -0.15, else Flat |

All signals are generated **using the previous day’s sentiment** to prevent look‑ahead.

## Methodology
1. **Data cleaning** – Drop rows where `Sentiment Lagged` is missing.
2. **Signal generation** – Apply rules to create position signals (-1, 0, +1).
3. **Strategy returns** – `Signal × Daily Return` (zero transaction costs assumed).
4. **Performance metrics** (per strategy):
   - Total return (net cumulative)
   - Annualised return (geometric)
   - Annualised volatility
   - Sharpe ratio (geometric return / volatility)
   - Maximum drawdown
   - Win rate (% of positive days)

## Key Findings
Among the 4 strategies, the contrarian sentiment strategy, where we buy when net sentiments of the previous day are> 0.15 and sell when < -0.15, shows the highest return based on the following back-test results:

| Metric | Buy & Hold | Contrarian (±0.15) |
|---|---|---|
| Total Return | 9.7% | **44.93%** |
| Ann. Return | 4.3% | **18.5** |
| Sharpe Ratio | 0.2907 | **0.9986** |

**Key insights:**
- The lag-1 sentiment correlation is negative (r = −0.063), indicating a mild contrarian signal.
- When the crowd is euphoric (net sentiment > +0.15), the index tends to underperform the next day — and vice versa.

**Limitations to be addressed**
- Missing sentiment data for some of the rows, affecting overall return rate and sentiment analysis due to returns not being independent and identically distributed
- Use of an arbitrary threshold for strategy testing (sensitivity analysis)

## Dependencies
pandas
numpy
matplotlib
openpyxl   # for reading .xlsx files

