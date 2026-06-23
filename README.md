# Multi-Factor Stock Ranking System
### Quantitative Equity Portfolio Construction & Backtesting Engine

![Python](https://img.shields.io/badge/Python-3.9+-blue)
![License](https://img.shields.io/badge/License-MIT-green)
![Status](https://img.shields.io/badge/Status-Complete-brightgreen)

> A production-quality multi-factor stock ranking system replicating the core methodology used by quantitative equity funds (AQR Capital Management, BlackRock, Dimensional Fund Advisors). Ranks S&P 500 stocks across Value, Quality, Momentum, and Profitability factors. Backtests top-decile portfolio against SPY over a 6-year window with full performance attribution.

---

## Highlights

- **4 investment factors**: Value (P/E, P/B, P/S, EV/EBITDA), Quality (D/E, Current Ratio), Momentum (12-1M, 6-1M, RSI), Profitability (ROE, ROA, Margins)
- **Cross-sectional Z-scoring** with winsorization to handle outliers
- **Quarterly rebalancing backtest** engine with dynamic momentum re-ranking
- **15+ performance metrics**: Sharpe, Sortino, Alpha, IR, Max Drawdown, Calmar, VaR
- **6 production charts**: Equity curve, factor heatmap, monthly returns heatmap, sector allocation, rolling Sharpe, full dashboard
- **Excel export**: 5-sheet performance summary workbook
- Runs 100% free in Google Colab using yfinance

---

## Quick Start

```python
# Google Colab — run this first
!pip install yfinance seaborn plotly openpyxl --quiet

# Then open and run:
# notebooks/multi_factor_stock_ranking.ipynb
```

Or run locally:
```bash
git clone https://github.com/yourusername/multi-factor-stock-ranking
cd multi-factor-stock-ranking
pip install -r requirements.txt
python src/multi_factor_stock_ranking.py
```

---

## Repository Structure

```
multi-factor-stock-ranking/
├── README.md
├── PROJECT_OVERVIEW.md          # Full methodology, architecture, build guide
├── requirements.txt
├── src/
│   └── multi_factor_stock_ranking.py   # Complete source code
├── outputs/
│   ├── *.csv                    # Factor scores, holdings, NAV series
│   ├── *.xlsx                   # Performance summary workbook
│   └── *.png                    # All charts
```

---

## Factor Methodology

| Factor | Metrics Used | Direction |
|--------|-------------|-----------|
| **Value** | P/E, P/B, P/S, EV/EBITDA | Lower is better (negated) |
| **Quality** | Current Ratio, Debt/Equity | Higher CR, lower D/E is better |
| **Momentum** | 12-1M return, 6-1M return, RSI-14 | Higher past return is better |
| **Profitability** | ROE, ROA, Gross/Operating/Net Margin | Higher is better |

**Composite Score** = 30% Value + 25% Quality + 25% Momentum + 20% Profitability

**Portfolio**: Top 10% of stocks by composite score, equal-weight, rebalanced quarterly.

---

## Academic References

- Fama & French (1992) — Value and Size Factors
- Jegadeesh & Titman (1993) — Momentum Effect
- Novy-Marx (2013) — Gross Profitability Premium
- Asness, Frazzini & Pedersen (2019) — Quality Minus Junk

---

## Performance Output (Sample)

```
Portfolio CAGR          :      XX.X%
Benchmark CAGR (SPY)    :      XX.X%
Alpha (annual)          :       X.X%
Sharpe Ratio            :       X.XXX
Max Drawdown            :     -XX.X%
Information Ratio       :       X.XXX
```
*(Actual results depend on universe size, time period, and data coverage)*

---

## Disclaimer

This project is for **educational purposes only**. It does not constitute financial advice. Past simulated performance does not guarantee future results. All data sourced from publicly available free APIs.

---

*Built by [Your Name] | Finance/Economics | UNC Chapel Hill*
