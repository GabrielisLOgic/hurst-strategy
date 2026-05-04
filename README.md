# Hurst Exponent — From Theory to Systematic Strategy

This repository documents a complete quantitative research project on the
Hurst exponent: from mathematical foundations to a systematic trading strategy
with rigorous statistical validation.

## Project Structure

| Notebook | Content |
|----------|---------|
| `01_hurst_theory.ipynb` | Theory, implementation from scratch, and real market analysis |
| `02_hurst_basket_model.ipynb` | Systematic basket strategy with full backtest and performance analysis |

## Key Results

### Hurst Exponent on Real Assets (2015-2025)

| Asset | R/S H | DFA alpha | Regime | Significant? |
|-------|-------|-----------|--------|-------------|
| S&P 500 ETF | 0.534 | 0.438 | Random | No (p=0.655) |
| Gold ETF | 0.549 | 0.486 | Random | No (p=0.530) |
| 20Y Treasury ETF | 0.544 | 0.502 | Random | No (p=0.530) |
| Bitcoin | 0.602 | 0.567 | Persistent | Yes (p=0.010) |

S&P 500, Gold, and Treasuries are statistically indistinguishable from
a random walk. Bitcoin is the only asset with statistically significant
long-range memory (p < 0.05).

### Basket Strategy Performance (2018-2025)

| Metric | Hurst Strategy | SPY Buy & Hold |
|--------|---------------|----------------|
| Total Return | 849.6% | 140.6% |
| CAGR | 25.6% | 9.3% |
| Sharpe Ratio | 0.88 | 0.39 |
| Sortino Ratio | 1.03 | 0.33 |
| Max Drawdown | -32.0% | -33.7% |

## Notebook 1 — Hurst Theory

Complete implementation of three Hurst estimators from scratch:

**Rescaled Range (R/S) Analysis**
The classical method introduced by Hurst (1951). Estimates H as the slope
of the log-log regression of R/S vs window size. Verified against synthetic
fBm series with known H values.

**Detrended Fluctuation Analysis (DFA)**
More robust than R/S for non-stationary series. Detrends each segment
with a local polynomial fit before computing the fluctuation function.
Consistently outperforms R/S for low-H (anti-persistent) series.

**Generalized Hurst Exponent (GHE)**
Extends H to arbitrary moment orders q. If H(q) varies with q, the series
is multifractal. The S&P 500 shows a spread of 0.163 across moment orders,
confirming multifractal structure.

**Rolling Hurst**
Computed over a sliding window to detect regime transitions over time.
Markets shift between trending and mean-reverting regimes — a single
static H misses this dynamics entirely.

**Surrogate Significance Test**
Permutation test to assess whether observed H is statistically significant.
500 shuffled surrogates establish the null distribution. Only Bitcoin
achieves p < 0.05 among the four assets tested.

## Notebook 2 — Basket Strategy

A systematic momentum strategy using the rolling Hurst exponent as the
primary asset selection signal.

**Strategy Logic**

1. Compute rolling Hurst (R/S, 63-day window) for 9 risk-on assets
2. Shift signals by 1 day to prevent lookahead bias
3. Every 10 trading days, rank assets by lagged Hurst
4. Select top 3 with H > 0.5
5. Equal-weight selected assets (33.3% each)
6. Allocate remaining weight to TLT (risk-off)
7. If no asset qualifies, go 100% TLT

**Asset Universe**

| Role | Assets |
|------|--------|
| Risk-on | BTC-USD, QQQ, SPY, AAPL, TSLA, GLD, NVDA, AMZN, XLE |
| Risk-off | TLT (20Y Treasury ETF) |

**Lookahead Bias Prevention**

All signals are shifted by 1 trading day. Hurst computed from returns
up to day t is only used for trading on day t+1. The backtest never
trades on the same bar that generated the signal.

## Methodology Notes

**What works well**
The Hurst exponent correctly identifies persistent assets and generates
a momentum signal with genuine directional content. The risk-off mechanism
with TLT provides natural hedging during equity drawdowns.

**Known limitations**
The strategy universe contains BTC, NVDA, and TSLA — the three
best-performing assets of 2018-2025. Alpha decomposition shows that
removing these three assets reduces Sharpe from 0.88 to 0.60.
The surrogate test yields p=0.18 at baseline — not statistically
significant. These limitations motivated the development of an enhanced
version with rigorous multi-tier validation, documented separately.

## Requirements
numpy
pandas
matplotlib
yfinance
scipy

Install with:
pip install numpy pandas matplotlib yfinance scipy

## References

1. Hurst, H.E. (1951). Long-term storage capacity of reservoirs.
   Transactions of the American Society of Civil Engineers, 116, 770-808.

2. Mandelbrot, B.B. and Van Ness, J.W. (1968). Fractional Brownian motions,
   fractional noises and applications. SIAM Review, 10(4), 422-437.

3. Mandelbrot, B.B. (1982). The Fractal Geometry of Nature. W.H. Freeman.

4. Peters, E.E. (1994). Fractal Market Analysis. Wiley.

5. Peng, C.K. et al. (1994). Mosaic organization of DNA nucleotides.
   Physical Review E, 49(2), 1685.

6. Di Matteo, T. et al. (2005). Long-term memories in stock market prices.
   Physica A, 355(1), 173-187.

7. Lopez de Prado, M. (2018). Advances in Financial Machine Learning. Wiley.
