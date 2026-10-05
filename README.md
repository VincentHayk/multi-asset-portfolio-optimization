# Multi-Asset Portfolio Optimization

Quantitative asset-allocation project combining **constrained Sharpe-ratio optimization**, **risk analytics**, **ESG-aware investing**, **European equities**, and **cryptocurrencies**.

The project studies two portfolio-construction approaches for a hypothetical Generation Z investor with a long investment horizon, moderate risk aversion, ESG preferences, interest in technology / digital assets, and an initial capital of **EUR 75,000**.

> **Important:** all performance figures shown below are historical / in-sample results from the original project. They are not forecasts or investment recommendations.

## Project scope

The analysis covers:

- daily market-data extraction with `yfinance`;
- return and covariance estimation;
- correlation analysis across asset classes;
- long-only portfolio optimization;
- Sharpe-ratio maximization with SLSQP;
- volatility and crypto-allocation constraints;
- two-stage / hierarchical asset allocation;
- Value at Risk (VaR) and Conditional Value at Risk (CVaR);
- maximum drawdown;
- ESG / PEA-aware security selection;
- crypto diversification;
- RSI-based timing analysis.

## Portfolio 1 — Baseline multi-asset allocation

The first universe contains:

- **URTH** — global developed-market equities;
- **BNDX** — international investment-grade bonds;
- **LQD** — U.S. investment-grade corporate bonds;
- **GLD** — gold;
- **BTC-USD** — Bitcoin.

The optimization searches over volatility and maximum-crypto constraints and maximizes

$$
\text{Sharpe}(w)=\frac{\mu_p-r_f}{\sigma_p}.
$$

Subject to:

$$
\sum_i w_i = 1,
\qquad
w_i \ge 0,
\qquad
\sigma_p \le \sigma_{\max},
\qquad
\sum_{i\in\mathcal{C}} w_i \le c_{\max}.
$$

In the original run, the selected constraint pair was approximately:

- maximum volatility: **15%**;
- maximum crypto weight: **18%**.

The corresponding in-sample portfolio produced approximately:

- annualized return: **18.35%**;
- annualized volatility: **14.97%**;
- Sharpe ratio: **1.10**.

The solution was strongly concentrated in gold over the studied period, illustrating an important limitation of pure in-sample mean-variance / Sharpe optimization: weights can become highly sensitive to the historical window.

## Portfolio 2 — ESG / PEA / technology / crypto allocation

The second portfolio introduces a top-down allocation:

- **40% ETFs**;
- **40% European equities**;
- **20% crypto-assets**.

The asset universe includes green bonds, ESG / sector ETFs, European technology and transition leaders, and a diversified crypto sleeve.

Weights are optimized **within each asset class** under a volatility cap, and then multiplied by the 40 / 40 / 20 top-down weights.

The original optimization selected:

- ETF volatility cap: **4%**;
- equity volatility cap: **20%**;
- crypto volatility cap: **75%**.

### Final in-sample allocation

| Asset | Weight |
|---|---:|
| EHYB.MI | 22.89% |
| IBE.MC | 21.03% |
| BGRN | 12.37% |
| SOL-USD | 10.24% |
| ASML.AS | 9.10% |
| BTC-USD | 5.99% |
| NOVO-B.CO | 5.31% |
| SU.PA | 4.56% |
| EXH9.DE | 3.51% |
| MATIC-USD | 2.51% |
| HBAR-USD | 1.26% |
| PUST.PA | 1.24% |

The report's final portfolio analysis gives approximately:

- annualized return: **12.4%**;
- annualized volatility: **15.1%**;
- Sharpe ratio: **0.7**;
- daily VaR 95%: **-1.38%**;
- daily CVaR 95%: **-2.0%**;
- maximum drawdown: **-10.4%**.

## Risk analytics

### Value at Risk

Historical daily VaR is computed from the empirical loss distribution:

$$
\mathrm{VaR}_{0.95}
=
Q_{0.95}(-R_p).
$$

### Conditional Value at Risk

CVaR measures the average loss conditional on exceeding VaR:

$$
\mathrm{CVaR}_{0.95}
=
\mathbb{E}
\left[
L \mid L \ge \mathrm{VaR}_{0.95}
\right].
$$

### Maximum drawdown

The drawdown process is computed from cumulative wealth relative to its running maximum. The maximum drawdown is the worst peak-to-trough loss observed over the sample.

## Optimization approach

The numerical optimization uses `scipy.optimize.minimize` with the **SLSQP** algorithm.

The project enforces:

- full investment;
- long-only weights;
- asset-class-specific volatility constraints;
- a maximum crypto exposure in Portfolio 1;
- a 40 / 40 / 20 top-down structure in Portfolio 2.

A small covariance ridge is used in the second optimization to improve numerical stability.

## RSI timing layer

The strategic allocation is complemented by a 14-period Relative Strength Index:

$$
\mathrm{RSI}
=
100-
\frac{100}{1+\mathrm{RS}},
\qquad
\mathrm{RS}
=
\frac{\text{Average Gain}}{\text{Average Loss}}.
$$

RSI is used only as a **secondary timing indicator**, not as the primary portfolio-construction signal.

## Repository structure

```text
multi-asset-portfolio-optimization/
├── notebooks/
│   └── multi_asset_portfolio_optimization.ipynb
├── README.md
├── requirements.txt
└── .gitignore
```

Generated Excel files are written to `outputs/` and are intentionally excluded from version control.

## Installation

```bash
pip install -r requirements.txt
```

Then open:

```text
notebooks/multi_asset_portfolio_optimization.ipynb
```

The notebook downloads historical prices directly from Yahoo Finance.

## Main Python stack

- Python
- NumPy
- pandas
- SciPy
- yfinance
- openpyxl
- XlsxWriter

## Reproducibility notes

The cleaned notebook fixes the syntax error present in the original Colab export and removes hard-coded `/content/...` file paths. Generated files now use a relative `outputs/` directory, so the notebook is easier to run both locally and in Colab.

The portfolio estimates remain sensitive to the chosen sample period and to historical estimates of means and covariances. Transaction costs, slippage, taxes, estimation uncertainty, and out-of-sample validation are not explicitly modeled.

## Attribution

**Original project contributors:** Elyes Bouziane, Nassima Mouaouia, Aya Karoum, Vincent Karakoseian.

**Repository maintained by:** Vincent Haïk Karakoseian.

## Disclaimer

This repository is an academic / portfolio project for educational purposes only. It does not constitute investment advice.
