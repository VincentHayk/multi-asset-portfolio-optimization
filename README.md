# Multi-Asset Portfolio Optimization

Quantitative asset-allocation project combining **constrained Sharpe-ratio optimization**, **risk analytics**, **ESG-aware investing**, **European equities**, and **cryptocurrencies**.

The project studies two portfolio-construction approaches for a hypothetical Generation Z investor with a long investment horizon, moderate risk aversion, ESG preferences, an interest in technology and digital assets, and an initial capital of **EUR 75,000**.

> **Important:** all performance figures presented in this repository are historical and in-sample results from the original project. They are not forecasts of future performance and should not be interpreted as investment recommendations.

## Project Scope

The analysis covers:

- daily market-data extraction with `yfinance`;
- return and covariance estimation;
- cross-asset correlation analysis;
- long-only portfolio optimization;
- Sharpe-ratio maximization using SLSQP;
- portfolio-volatility constraints;
- crypto-exposure constraints;
- two-stage asset allocation;
- Value at Risk (VaR);
- Conditional Value at Risk (CVaR);
- maximum drawdown;
- ESG and PEA-aware security selection;
- cryptocurrency diversification;
- RSI-based timing analysis.

---

## Portfolio 1 — Baseline Multi-Asset Allocation

The first investment universe contains:

- **URTH** — global developed-market equities;
- **BNDX** — international investment-grade bonds;
- **LQD** — U.S. investment-grade corporate bonds;
- **GLD** — gold;
- **BTC-USD** — Bitcoin.

The objective is to maximize the portfolio Sharpe ratio.

**Sharpe Ratio = (Expected Portfolio Return - Risk-Free Rate) / Portfolio Volatility**

The optimization is subject to the following constraints:

- full investment: `sum(weights) = 1`;
- long-only allocation: `weight_i >= 0`;
- portfolio volatility must remain below a predefined maximum level;
- total cryptocurrency exposure must remain below a predefined maximum level.

A grid search is performed over different volatility and cryptocurrency-exposure limits in order to identify the smallest constraints compatible with the maximum observed Sharpe ratio.

### Selected Constraints

In the original run, the selected configuration was approximately:

- maximum annualized volatility: **15%**;
- maximum cryptocurrency exposure: **18%**.

### In-Sample Results

The corresponding optimized portfolio produced approximately:

| Metric | Result |
|---|---:|
| Annualized return | 18.35% |
| Annualized volatility | 14.97% |
| Sharpe ratio | 1.10 |
| Crypto exposure | 17.97% |

The optimized allocation was strongly concentrated in gold over the studied period.

This result illustrates an important limitation of pure historical Sharpe-ratio optimization: portfolio weights may become highly sensitive to the selected estimation window and to unusually strong historical performance from individual assets.

The bond ETFs BNDX and LQD received negligible or zero optimal allocations because their historical risk-adjusted performance over the analyzed period was weaker than that of the other available asset classes.

---

## Portfolio 2 — ESG, PEA, Technology and Crypto Allocation

The second portfolio introduces a more structured top-down allocation designed to better reflect the investor profile.

The strategic allocation is fixed at:

- **40% ETFs**;
- **40% European equities**;
- **20% cryptocurrencies**.

The investable universe includes:

- green bonds;
- ESG-oriented bond ETFs;
- European equity ETFs;
- technology ETFs;
- defensive-sector ETFs;
- European technology leaders;
- energy-transition companies;
- healthcare companies;
- major cryptocurrencies;
- ESG-oriented and energy-efficient blockchain projects.

The optimization is performed separately within each asset class.

The optimized intra-class weights are then multiplied by the strategic **40 / 40 / 20** allocation.

### Selected Volatility Constraints

The original optimization selected:

- ETF volatility cap: **4%**;
- equity volatility cap: **20%**;
- cryptocurrency volatility cap: **75%**.

---

## Final In-Sample Allocation

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

The resulting portfolio combines defensive fixed-income exposure, European equities, technology, energy-transition themes and cryptocurrencies.

---

## Final Portfolio Metrics

The original analysis produced approximately:

| Metric | Result |
|---|---:|
| Annualized return | 12.4% |
| Annualized volatility | 15.1% |
| Sharpe ratio | 0.70 |
| Daily VaR at 95% | 1.38% loss threshold |
| Daily CVaR at 95% | approximately 2.0% |
| Maximum drawdown | -10.4% |

The portfolio does not seek to maximize raw return alone.

Its objective is to obtain an attractive **return per unit of risk** while combining several performance drivers and limiting excessive concentration in a single asset class.

---

## Risk Analytics

### Value at Risk

Historical Value at Risk is estimated directly from the empirical distribution of daily portfolio losses.

In simple terms:

`VaR 95% = 95th percentile of the daily portfolio loss distribution`

A daily VaR of approximately **1.38%** means that, historically, around 95% of observed daily losses remained below this threshold.

Only approximately 5% of observations generated a larger daily loss.

### Conditional Value at Risk

Conditional Value at Risk measures the average loss in the tail of the distribution once the VaR threshold has already been exceeded.

In simple terms:

`CVaR 95% = Average loss conditional on Loss >= VaR 95%`

CVaR therefore complements VaR by measuring the severity of extreme losses rather than only identifying a loss threshold.

### Maximum Drawdown

Maximum drawdown measures the largest historical decline in portfolio value from a previous peak to a subsequent trough.

It is used as an intuitive measure of the maximum historical loss experienced by an investor before the portfolio recovered.

---

## Correlation Analysis

Correlation matrices are computed for:

- the initial multi-asset universe;
- the ETF universe;
- the European equity universe;
- the cryptocurrency universe;
- the final portfolio.

The objective is to identify assets whose return drivers are sufficiently different to provide genuine diversification.

The analysis notably highlights the defensive role of bond ETFs through relatively low correlations with equities and cryptocurrencies.

Within the final portfolio, most cross-asset correlations remain low to moderate, while correlations within the cryptocurrency sleeve are naturally higher.

---

## Optimization Methodology

The numerical optimization uses:

`scipy.optimize.minimize`

with the **SLSQP — Sequential Least Squares Programming** algorithm.

The optimization framework includes:

- long-only weights;
- full investment;
- volatility constraints;
- cryptocurrency-exposure constraints in Portfolio 1;
- asset-class-specific constraints in Portfolio 2;
- Sharpe-ratio maximization.

A small covariance-matrix ridge is also introduced in the second optimization in order to improve numerical stability.

---

## Two-Stage Allocation Framework

Portfolio 2 uses a two-stage construction methodology.

### Stage 1 — Strategic Allocation

Capital is first divided between asset classes:

| Asset Class | Strategic Weight |
|---|---:|
| ETFs | 40% |
| European equities | 40% |
| Cryptocurrencies | 20% |

### Stage 2 — Intra-Class Optimization

The weights of individual assets inside each asset class are then optimized by maximizing the Sharpe ratio under the corresponding volatility constraint.

The final portfolio weight of an asset is therefore:

`Final Weight = Strategic Asset-Class Weight x Optimized Intra-Class Weight`

This approach prevents the optimization from allocating the entire portfolio to whichever asset class happened to exhibit the strongest historical Sharpe ratio.

---

## ESG and Thematic Allocation

The second portfolio incorporates several thematic dimensions.

### Sustainable Fixed Income

The portfolio considers ESG-oriented and green-bond instruments such as:

- **BGRN**;
- **EHYB.MI**.

These assets are used both for diversification and as lower-volatility components of the portfolio.

### European Technology and Innovation

The equity universe includes companies such as:

- **ASML**;
- **SAP**;
- **Schneider Electric**.

These companies provide exposure to semiconductors, enterprise software, industrial automation, electrification and digital transformation.

### Energy Transition and Defensive ESG Exposure

The analysis also considers companies such as:

- **Iberdrola**;
- **Veolia**;
- **Novo Nordisk**.

The objective is to combine growth opportunities with more defensive and idiosyncratic performance drivers.

### Cryptocurrency Diversification

The crypto universe extends beyond Bitcoin and includes several blockchain ecosystems and infrastructure projects.

The analyzed universe includes assets such as:

- Bitcoin;
- Ethereum;
- Solana;
- Cardano;
- Polkadot;
- Hedera;
- Polygon;
- Chainlink.

The objective is not only to seek higher expected returns, but also to diversify technological exposure within the digital-asset sleeve.

---

## RSI Timing Layer

The strategic portfolio allocation is complemented by a **14-period Relative Strength Index (RSI)**.

The RSI is calculated from average positive and negative price movements.

The calculation can be summarized as:

`RS = Average Gain / Average Loss`

and:

`RSI = 100 - 100 / (1 + RS)`

The interpretation used in the project is:

- `RSI > 70` — potentially overbought;
- `RSI < 30` — potentially oversold;
- `30 <= RSI <= 70` — broadly neutral.

The RSI is **not used as the primary portfolio-construction signal**.

It acts only as a complementary timing indicator after the strategic portfolio has already been constructed.

---

## Annual Re-Optimization

The project also considers periodic portfolio rebalancing.

At the end of each investment year:

1. historical returns are updated;
2. asset statistics are recalculated;
3. the covariance structure is updated;
4. Sharpe-ratio optimization is performed again;
5. portfolio weights are adjusted accordingly.

This allows the allocation to adapt progressively to changing historical risk and return characteristics.

---

## Repository Structure

```text
multi-asset-portfolio-optimization/
├── notebooks/
│   └── multi_asset_portfolio_optimization.ipynb
├── README.md
├── requirements.txt
└── .gitignore
```

Generated Excel files are written to the local `outputs/` directory and are intentionally excluded from version control.

---

## Installation

Install the required Python packages with:

```bash
pip install -r requirements.txt
```

Then open:

```text
notebooks/multi_asset_portfolio_optimization.ipynb
```

Historical market prices are downloaded directly from Yahoo Finance through `yfinance`.

---

## Main Python Stack

- Python
- NumPy
- pandas
- SciPy
- yfinance
- openpyxl
- XlsxWriter
- Jupyter Notebook / Google Colab

---

## Reproducibility Notes

The repository version of the notebook was cleaned for easier execution outside Google Colab.

In particular:

- the syntax error present in the original notebook export was corrected;
- hard-coded `/content/...` paths were removed;
- generated files now use a relative `outputs/` directory;
- unnecessary Colab-specific download outputs were removed;
- code comments and project documentation were standardized in English.

The results remain dependent on historical market data and therefore on the selected sample period.

The analysis does not explicitly incorporate:

- transaction costs;
- bid-ask spreads;
- slippage;
- taxation;
- parameter-estimation uncertainty;
- turnover penalties;
- out-of-sample optimization;
- forward-looking expected-return models.

These elements represent natural extensions of the project.

---

## Attribution

**Original project contributors:** Elyes Bouziane, Nassima Mouaouia, Aya Karoum, Vincent Haïk Karakoseian.

**Repository maintained by:** Vincent Haïk Karakoseian.

---

## Disclaimer

This repository is an academic and portfolio project provided for educational purposes only.

It does not constitute investment advice, a solicitation, or a recommendation to buy or sell any financial instrument.
