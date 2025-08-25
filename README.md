# MSDS 451 Term Project
## Regime-Aware Systematic Multi-Asset Strategy (Macro Sentiment Adaptive Allocation ETF)

**Author:** Ben Caldwell · **Institution:** Northwestern University  
**Dates analyzed:** 2021-10-01 → 2025-08-14 (API-limited)
Distributional evaluation via regime-conditional Monte Carlo

---

### Overview
I built a rules-based, regime-aware multi-asset strategy that blends machine-learning signals (per-regime ensembles with isotonic calibration and MC-dropout uncertainty) with a risk-budgeted quadratic program (Ledoit–Wolf covariance, turnover penalties, optional TE/beta band). Trading frictions and fee modeling are explicit and reproducible.

**Why only ~5 years of raw history?** API request/rate limits on programmatic sources. I mitigate this by (1) incorporating macro series from FRED for regime labeling and (2) running a regime-conditional block-bootstrap to approximate long-run behavior. Extending to 1999–2024 is planned with alternate vendors.

---

### Key Results (Champion configuration)
- **CAGR:** 12.48% vs. SPY 8.03%  
- **Volatility:** 11.93% vs. SPY 13.55%  
- **Sharpe:** 1.05 vs. SPY 0.64  
- **Max Drawdown:** −14.50% vs. SPY −18.70%  
- **Factor footprint:** β ≈ 0.57, annualized α ≈ 3.76%, TE ≈ 10%, IR ≈ 0.38  
- **Signal quality:** mean Rank-IC ≈ 0.035 (IR ≈ 0.13; n = 96)  
- **Monte Carlo (regime-conditional):** median CAGR ≈ 7.5%, 5–95% ≈ −0.01% to 15.69%

---

### Figures included
- `Screenshot 2025-08-25 at 12.27.51 PM.png` — Weight treemap  
- `download (10).png` — Cumulative returns (strategy vs. benchmark)  
- `download (11).png` — Strategy drawdown  
- `download (12).png` — Portfolio weights (top by usage)  
- `download (13).png` — Monte Carlo CAGR histogram  
- `download (14).png` — Champion cumulative return  
- `download (15).png` — Weights (recent)

---

### Data & Features (brief)
- **Universe:** 26 liquid ETFs/crypto (SPY, QQQ, IWM, EFA, EEM, sector ETFs, TLT/IEF/LQD/HYG, GLD/SLV/DBC/USO/UNG, VNQ, BTC-USD, ETH-USD)  
- **Macro (FRED):** `GS10`, `TB3MS`, `BAMLH0A0HYM2`, `DTWEXBGS`, `CPIAUCSL` — forward-filled to business days; term spread, HY OAS z-score, USD returns, CPI YoY derived  
- ~90 technical/macro features per asset; regimes = Trend (SMA200) × Volatility (percentiles)

---

### How to run
1. **Clone** the repo  
2. **Install requirements:**  
   - `conda env create -f env/environment.yml && conda activate regime-asset`  
   - *or* `pip install -r env/requirements.txt`  
   - Core deps: `numpy`, `pandas`, `scikit-learn`, `matplotlib`, `cvxpy` (+ solver), `statsmodels`, `pandas-datareader`, `yfinance` (optional), and either PyTorch or Keras/TensorFlow for the ML baseline
3. **Run the notebook** in `notebooks/` to reproduce the pipeline end-to-end (data ingest → features → regimes → training → backtest → Monte Carlo → figures/tables/logs)  

---

### Notes on methodology
- Purged & embargoed walk-forward validation to avoid leakage 
- Per-regime ensembles with isotonic calibration; MC-dropout for uncertainty shrinkage  
- QP allocator with covariance shrinkage, per-asset caps, turnover penalty, and optional TE/beta constraint  
- Fees: daily management accrual (1/252) and optional annual performance fee on excess (illustrative)  
- Regime-conditional block bootstrap preserves state mix and serial dependence

---

### Disclaimer
Educational research only. Backtests and simulations are not indicative of future results. No investment advice.

