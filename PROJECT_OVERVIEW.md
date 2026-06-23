# Multi-Factor Stock Ranking System
### Quantitative Equity Portfolio Construction & Backtesting Engine

---

## 1. Project Overview

This project builds a **production-grade, multi-factor quantitative equity system** that ranks S&P 500 stocks across four academically validated investment factors — Value, Quality, Momentum, and Profitability — constructs a top-decile long portfolio, and backtests performance against the S&P 500 over a six-year window.

The methodology directly mirrors how **quantitative equity funds** (AQR Capital Management, BlackRock Scientific Equity, Dimensional Fund Advisors, Two Sigma) systematically select stocks. Factor investing manages **trillions of dollars** in assets globally.

**What the system does end-to-end:**
- Downloads the S&P 500 universe and financial data via yfinance
- Computes cross-sectional factor signals (Value, Quality, Momentum, Profitability)
- Winsorizes outliers, Z-scores each signal, and blends them into a composite rank
- Selects the top-decile portfolio, rebalances quarterly
- Backtests vs. SPY with full performance attribution
- Generates six production-quality charts and a multi-sheet Excel summary

---

## 2. Real-World Finance Use Case

Factor investing is not academic — it drives real capital allocation at scale:

| Firm | AUM | Factor Approach |
|------|-----|----------------|
| AQR Capital Management | ~$100B | Multi-factor (Value, Momentum, Quality, Carry) |
| BlackRock Scientific Equity | ~$200B+ | Systematic factor tilts |
| Dimensional Fund Advisors | ~$700B | Value, Profitability, Small-size factors |
| Two Sigma | ~$60B | Quantitative signals including factor models |
| Renaissance Technologies | ~$100B | Systematic factor-based strategies |

**Why these four factors?**

| Factor | Economic Rationale | Academic Source |
|--------|-------------------|----------------|
| **Value** | Cheap stocks compensate for distress risk | Fama & French (1992) |
| **Momentum** | Trend continuation; behavioral under-reaction | Jegadeesh & Titman (1993) |
| **Quality** | Financially sound firms outperform over time | Asness, Frazzini, Pedersen (2019) |
| **Profitability** | Profitable firms earn higher expected returns | Novy-Marx (2013) |

---

## 3. System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    DATA COLLECTION LAYER                    │
│  ┌───────────────┐  ┌───────────────┐  ┌─────────────────┐ │
│  │ S&P 500 Tickers│  │ Price History │  │  Fundamentals   │ │
│  │  (Wikipedia)  │  │  (yfinance)   │  │  (yfinance.info)│ │
│  └───────────────┘  └───────────────┘  └─────────────────┘ │
└─────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                  FEATURE ENGINEERING LAYER                  │
│  ┌─────────┐  ┌─────────┐  ┌──────────┐  ┌─────────────┐  │
│  │  Value  │  │ Quality │  │ Momentum │  │Profitability│  │
│  │ P/E,P/B │  │ D/E,CR  │  │ 12-1M,   │  │ ROE,ROA,    │  │
│  │ P/S,EV/ │  │         │  │ 6-1M,RSI │  │ Margins     │  │
│  │ EBITDA  │  │         │  │          │  │             │  │
│  └────┬────┘  └────┬────┘  └────┬─────┘  └──────┬──────┘  │
│       └────────────┴────────────┴────────────────┘         │
│                   Winsorize → Z-Score → Weighted Average    │
│                        ▼  COMPOSITE SCORE  ▼                │
└─────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────┐
│               PORTFOLIO CONSTRUCTION LAYER                  │
│     Top 10% by Composite Score → Equal-Weight Portfolio     │
│              Quarterly Rebalancing Schedule                 │
└─────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────┐
│                   BACKTESTING ENGINE                        │
│    6-Year Window │ Quarterly Rebalance │ SPY Benchmark      │
│    Dynamic Momentum Re-Ranking at Each Rebalance Date       │
└─────────────────────────────────────────────────────────────┘
                            ▼
┌─────────────────────────────────────────────────────────────┐
│              PERFORMANCE & VISUALIZATION LAYER              │
│  Sharpe │ Sortino │ Alpha │ IR │ Max DD │ Calmar │ VaR      │
│  Equity Curve │ Heatmap │ Dashboard │ Sector Chart          │
└─────────────────────────────────────────────────────────────┘
```

---

## 4. Required APIs & Data Sources

| Source | What It Provides | Access |
|--------|-----------------|--------|
| **Yahoo Finance** (yfinance) | Price history, fundamentals (P/E, P/B, ROE, margins, D/E), market cap | Free, no API key |
| **Wikipedia** | S&P 500 constituent list with sectors | Free, scraped via `pd.read_html` |
| **SEC EDGAR** | Raw 10-K/10-Q financial statements | Free (optional upgrade) |
| **Financial Modeling Prep** | Premium fundamentals, cleaner data | Free tier available, paid for scale |

> **Note:** This project is designed to run 100% free with yfinance and Wikipedia.

---

## 5. Required Python Libraries

```txt
# Core
pandas>=2.0.0
numpy>=1.24.0
scipy>=1.10.0

# Financial Data
yfinance>=0.2.28

# Visualization
matplotlib>=3.7.0
seaborn>=0.12.0
plotly>=5.14.0        # optional interactive charts

# Export
openpyxl>=3.1.0       # Excel export
```

Install in Colab with:
```python
!pip install yfinance seaborn plotly openpyxl --quiet
```

---

## 6. Folder / File Structure

Even though this project runs in Google Colab, structure it on GitHub as:

```
multi-factor-stock-ranking/
│
├── README.md                          # GitHub landing page
├── PROJECT_OVERVIEW.md                # This file
├── requirements.txt                   # Python dependencies
│
├── notebooks/
│   └── multi_factor_stock_ranking.ipynb    # Main Colab notebook
│
├── src/
│   ├── multi_factor_stock_ranking.py  # Clean Python module version
│   ├── data_collection.py             # Universe + price + fundamentals
│   ├── factor_engineering.py          # All factor construction logic
│   ├── backtesting.py                 # Backtest engine
│   ├── performance.py                 # Metrics calculations
│   └── visualizations.py             # All chart generation
│
├── outputs/
│   ├── factor_scores_full.csv         # All stocks ranked
│   ├── long_portfolio_holdings.csv    # Current portfolio
│   ├── backtest_nav_series.csv        # Daily NAV time series
│   ├── performance_summary.xlsx       # Multi-sheet Excel report
│   ├── chart_equity_curve.png
│   ├── chart_factor_heatmap.png
│   ├── chart_score_distribution.png
│   ├── chart_sector_allocation.png
│   ├── chart_rolling_metrics.png
│   └── chart_dashboard.png
│
└── docs/
    └── factor_methodology.md          # Factor definitions & sources
```

---

## 7. Step-by-Step Build Guide

### Phase 1 — Environment Setup (Day 1, ~2 hours)
1. Open Google Colab (`colab.research.google.com`)
2. Install yfinance, seaborn, plotly, openpyxl
3. Import all libraries; set global CONFIG dictionary
4. Test yfinance with a single ticker (AAPL) to confirm access

### Phase 2 — Data Collection (Day 1–2, ~4 hours)
5. Scrape S&P 500 tickers from Wikipedia using `pd.read_html`
6. Build batch price downloader with error handling
7. Build fundamental data fetcher using `yf.Ticker(t).info`
8. Validate data: check null percentages, date ranges, data types

### Phase 3 — Feature Engineering (Day 2–3, ~4 hours)
9. Build `winsorize_series()` — clip at 5th/95th percentile
10. Build `z_score_series()` — standardize cross-sectionally
11. Construct each factor score (Value, Quality, Momentum, Profitability)
12. Combine into weighted composite score
13. Add percentile rank column

### Phase 4 — Portfolio Construction (Day 3, ~2 hours)
14. Select top-decile stocks by composite score
15. Apply equal-weight scheme
16. Build decile breakdown table (D1–D10)

### Phase 5 — Backtesting Engine (Day 4–5, ~5 hours)
17. Download SPY as benchmark
18. Generate quarterly rebalance date schedule
19. Build simulation loop: re-rank → select → compute returns → advance
20. Concatenate period returns; remove duplicates
21. Compute cumulative NAV series for portfolio and SPY
22. Track turnover at each rebalance

### Phase 6 — Performance Analysis (Day 5, ~2 hours)
23. Code `calculate_performance_metrics()` — all 15 metrics
24. Run for portfolio and benchmark
25. Print side-by-side comparison

### Phase 7 — Visualizations (Day 6, ~4 hours)
26. Equity curve + drawdown chart (2-panel)
27. Factor heatmap — top 30 stocks × 4 factors
28. Score distribution + decile bar chart
29. Sector allocation donut chart
30. Rolling Sharpe + rolling volatility
31. Six-panel performance dashboard with monthly returns heatmap

### Phase 8 — Export & Polish (Day 7, ~2 hours)
32. Export CSVs and multi-sheet Excel
33. Clean up code comments and cell headers
34. Upload to GitHub with README and sample output screenshots

---

## 8. Data Collection Pipeline

```
Wikipedia (HTML table)
    ↓  pd.read_html()
S&P 500 Tickers (500 rows × 4 columns: ticker, name, sector, industry)
    ↓  yf.download(batch=50)
Price Matrix (1,500+ trading days × 500 stocks, adjusted close)
    ↓  quality filter (80% coverage minimum)
    ↓  yf.Ticker(t).info for each ticker
Fundamentals Table (500 stocks × 15 fields: P/E, P/B, P/S, ROE, ROA, margins, D/E, ...)
    ↓  missing-data filter (60% populated minimum)
Clean Dataset (~400–480 stocks)
```

**Rate limiting strategy:**
- Batch download (50 tickers per call) for prices
- 0.3-second delay between individual fundamentals calls
- Try/except wrapper on every API call; failed tickers logged and skipped

---

## 9. Data Cleaning & Feature Engineering

### Outlier Handling
```python
# Winsorize at 5th/95th percentile — removes data entry errors and
# extreme outliers without deleting observations
def winsorize_series(s, lower=0.05, upper=0.95):
    lo, hi = s.quantile(lower), s.quantile(upper)
    return s.clip(lo, hi)
```

### Cross-Sectional Z-Scoring
```python
# Normalize all stocks to same scale so factors are comparable
def z_score_series(s):
    return (s - s.mean()) / s.std(ddof=1)
```

### Directional Alignment
| Factor | Direction | Why |
|--------|-----------|-----|
| P/E, P/B, P/S, EV/EBITDA | **Negate** z-score | Lower ratio = cheaper = better |
| Debt/Equity | **Negate** z-score | Lower leverage = safer |
| Current Ratio | **Positive** z-score | Higher liquidity = better |
| ROE, ROA, Margins | **Positive** z-score | Higher profitability = better |
| 12-1M, 6-1M Momentum | **Positive** z-score | Higher past return = stronger trend |
| 1-Month Momentum | **Negate** (partial) | Short-term reversal effect |

### Composite Score Construction
```python
composite = (
    value_score         * 0.30 +
    quality_score       * 0.25 +
    momentum_score      * 0.25 +
    profitability_score * 0.20
)
```

---

## 10. Core Models & Algorithms

### Factor Model
**Cross-sectional multi-factor model** (Fama-French extended):

```
E[R_i] = α + β₁(Value_i) + β₂(Quality_i) + β₃(Momentum_i) + β₄(Profitability_i) + ε_i
```

Where each factor loading is the Z-scored signal described above.

### Portfolio Selection Algorithm
1. Rank all N stocks by composite score (descending)
2. Select top 10% (top decile) → N/10 stocks
3. Assign equal weight: `w_i = 1 / (N/10)` for each selected stock
4. Hold until next quarterly rebalance date

### Backtest Simulation
- **Look-ahead bias prevention:** At each rebalance date, only data available *up to* that date is used for momentum re-ranking
- **Survivorship bias:** Mitigated by using the current S&P 500 list (acknowledged limitation — a professional implementation would use point-in-time constituent data)
- **Transaction costs:** Not modeled (acknowledged limitation — real funds deduct ~5–10bps per trade)

### Z-Score Calculation (cross-sectional)
At each rebalance date `t`, for factor `f` across all stocks `i`:
```
z_{i,f,t} = (x_{i,f,t} - μ_{f,t}) / σ_{f,t}
```
Where `μ` and `σ` are computed across the universe at that date.

---

## 11. Visualizations & Dashboard Components

| Chart | Description | Key Insight |
|-------|-------------|-------------|
| **Equity Curve** | Portfolio vs SPY NAV from $1.00 | Visual performance comparison over full period |
| **Drawdown Chart** | Rolling drawdown series below zero line | Worst loss periods and recovery time |
| **Factor Heatmap** | Top 30 stocks × 4 factors, color = z-score | Shows which factors drive top picks |
| **Score Distribution** | KDE + histogram of composite scores | Top-decile threshold visualization |
| **Decile Bar Chart** | Average score by decile D1–D10 | Factor monotonicity — does factor spread make sense? |
| **Sector Allocation** | Donut chart of portfolio sector weights | Concentration risk identification |
| **Rolling Sharpe** | 126-day rolling Sharpe ratio | Time-varying risk-adjusted performance |
| **Rolling Volatility** | 126-day rolling annualized vol | Regime changes in risk |
| **Monthly Returns Heatmap** | Calendar-year grid of monthly returns | Seasonal patterns, drawdown months |
| **Return Distribution** | Histogram + normal fit + VaR line | Fat tail risk assessment |
| **Turnover Chart** | Quarterly portfolio turnover % | Trading cost efficiency |

---

## 12. Performance Metrics

| Metric | Formula | Interpretation |
|--------|---------|----------------|
| **Total Return** | `(Final NAV / 1.00) - 1` | Cumulative gain over full period |
| **CAGR** | `(1 + TR)^(1/years) - 1` | Annualized compound return |
| **Volatility** | `σ_daily × √252` | Annualized standard deviation |
| **Sharpe Ratio** | `(CAGR - Rf) / Vol` | Risk-adjusted return per unit of total risk |
| **Sortino Ratio** | `(CAGR - Rf) / Downside_Vol` | Risk-adjusted return penalizing only losses |
| **Max Drawdown** | `min((NAV / rolling_max) - 1)` | Worst peak-to-trough loss |
| **Calmar Ratio** | `CAGR / |Max DD|` | Return per unit of drawdown risk |
| **Beta** | `Cov(R_p, R_m) / Var(R_m)` | Market sensitivity |
| **Alpha (Jensen's)** | `CAGR - [Rf + β(Rm - Rf)]` | Excess return vs CAPM prediction |
| **Information Ratio** | `(R_p - R_b) / TE` | Consistency of outperformance |
| **Tracking Error** | `σ(R_p - R_b) × √252` | Deviation from benchmark |
| **Win Rate** | `% days with R_p > 0` | Frequency of positive returns |
| **VaR (95%)** | `5th percentile of daily returns` | Daily loss not exceeded 95% of days |
| **Skewness** | Third moment | Return distribution asymmetry |
| **Kurtosis** | Fourth moment | Fat tail risk indicator |

---

## 13. Final Deliverables

| Deliverable | Format | Description |
|-------------|--------|-------------|
| `multi_factor_stock_ranking.py` | Python | Complete annotated source code |
| `multi_factor_stock_ranking.ipynb` | Colab Notebook | Same code, cell-by-cell with outputs |
| `factor_scores_full.csv` | CSV | All stocks with individual + composite scores |
| `long_portfolio_holdings.csv` | CSV | Current top-decile portfolio with weights |
| `backtest_nav_series.csv` | CSV | Daily NAV, returns for portfolio and SPY |
| `performance_summary.xlsx` | Excel | 5-sheet summary: metrics, top 50, portfolio, NAV, turnover |
| `chart_equity_curve.png` | PNG | Equity curve + drawdown (2-panel) |
| `chart_factor_heatmap.png` | PNG | Factor score heatmap |
| `chart_score_distribution.png` | PNG | Score distribution + decile breakdown |
| `chart_sector_allocation.png` | PNG | Portfolio sector allocation donut |
| `chart_rolling_metrics.png` | PNG | Rolling Sharpe + rolling volatility |
| `chart_dashboard.png` | PNG | Six-panel performance dashboard |
| `PROJECT_OVERVIEW.md` | Markdown | This document |

---

## 14. Resume Description

### Bullet Point (2-line, standard resume format):
> **Multi-Factor Stock Ranking System** | Python, yfinance, pandas, NumPy, Matplotlib
> Built a quantitative equity ranking engine modeling S&P 500 stocks across Value, Quality, Momentum, and Profitability factors; backtested top-decile portfolio over 6 years against SPY benchmark, computing Sharpe ratio, Jensen's alpha, max drawdown, and information ratio.

### Extended Project Description (LinkedIn / GitHub):
> Designed and implemented a multi-factor stock ranking and backtesting system replicating the core methodology used by quantitative equity funds (AQR, BlackRock, Dimensional). The system downloads S&P 500 price and fundamental data, constructs cross-sectional Z-scored factor signals across four academically validated factors, combines them into a weighted composite score, selects a top-decile equal-weight portfolio, and simulates quarterly rebalancing over a 6-year window. Includes 15+ performance metrics (Sharpe, Sortino, Calmar, IR, Alpha, Max Drawdown, VaR) and six production-quality visualizations including a monthly returns heatmap, factor heatmap, rolling Sharpe, and multi-panel dashboard.

### Skills to List:
- Factor Investing, Alpha Generation, Portfolio Construction
- Quantitative Finance, Backtesting, Risk-Adjusted Performance Analysis
- Python (pandas, NumPy, SciPy, yfinance, Matplotlib, Seaborn)
- Z-Score Normalization, Winsorization, Cross-Sectional Factor Models
- Financial Statement Analysis (P/E, P/B, ROE, Margins, D/E)

---

## 15. Potential Upgrades

### Level 1 — Improved Data (Lower effort)
| Upgrade | Implementation |
|---------|---------------|
| **Financial Modeling Prep API** | Better fundamental data, cleaner earnings history |
| **Sector-neutral ranking** | Z-score within sectors to avoid sector concentration |
| **Point-in-time constituents** | Avoid survivorship bias using historical S&P 500 membership |
| **Earnings yield** | Use EBITDA/EV instead of or alongside P/E |

### Level 2 — Better Modeling (Intermediate)
| Upgrade | Implementation |
|---------|---------------|
| **Transaction cost model** | Deduct 5–15bps per trade based on market cap / liquidity |
| **Long-short portfolio** | Long top decile + short bottom decile = market-neutral |
| **Factor timing model** | Weight factors dynamically based on macro regime (e.g., VIX level) |
| **Machine learning ranking** | XGBoost or LightGBM to learn optimal factor weights from data |
| **Residual Momentum** | Strip out industry effect from momentum signal |

### Level 3 — Professional Infrastructure (Advanced)
| Upgrade | Implementation |
|---------|---------------|
| **Risk parity weighting** | Weight stocks inversely to volatility contribution |
| **Fama-MacBeth regression** | Cross-sectional regression to test factor significance each period |
| **IC (Information Coefficient)** | Measure rank correlation of factor scores with next-period returns |
| **Alphalens integration** | Professional factor analysis toolkit (Quantopian open-source) |
| **Live paper trading** | Connect to Alpaca Markets API for paper trading execution |
| **Streamlit dashboard** | Deploy interactive web dashboard for real-time scores |
| **PostgreSQL backend** | Store historical factor scores and backtest results in database |

---

## Acknowledgments

**Academic Foundations:**
- Fama, E. F., & French, K. R. (1992). "The Cross-Section of Expected Stock Returns." *Journal of Finance.*
- Jegadeesh, N., & Titman, S. (1993). "Returns to Buying Winners and Selling Losers." *Journal of Finance.*
- Novy-Marx, R. (2013). "The Other Side of Value: The Gross Profitability Premium." *Journal of Financial Economics.*
- Asness, C., Frazzini, A., & Pedersen, L. (2019). "Quality Minus Junk." *Review of Accounting Studies.*

---

*Built as a quantitative finance portfolio project demonstrating factor investing, Python data engineering, and systematic portfolio construction. Suitable for GitHub, finance internship applications, and UNC Kenan-Flagler undergraduate finance coursework.*
