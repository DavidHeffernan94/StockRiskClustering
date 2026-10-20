# StockRiskClustering

Do stocks cluster by sector, or by risk profile?

Twenty US-listed equities are characterised on five risk and return measures
over ten years of daily data, then clustered using four algorithms. The
resulting clusters are compared against sector labels.

**Finding:** they are unrelated. Adjusted Rand index against sector ranges from
0.062 to 0.152 across four algorithms, against 0 for random labelling. The
algorithms agree with each other - k-means and hierarchical clustering produce
identical partitions - but the structure they find is risk, not industry.

## Method

**Data.** Daily adjusted closing prices for 20 tickers from the Yahoo Finance
API, September 2016 to September 2026. The S&P 500 serves as the market
benchmark for beta.

**Features.** Five standard measures per stock:

| Feature | Definition |
| --- | --- |
| Annual return | Mean daily return annualised over 252 trading days |
| Annual volatility | Standard deviation of daily returns, annualised |
| Sharpe ratio | Excess return over a 3% risk-free rate per unit of volatility |
| Beta | Covariance with the S&P 500 divided by market variance |
| Maximum drawdown | Largest peak-to-trough decline over the period |

**Algorithms.** K-means, hierarchical (Ward linkage), k-medoids and DBSCAN, each
evaluated on silhouette score, Davies-Bouldin index, and adjusted Rand index
against sector membership.

## Results

| Algorithm | Clusters | Silhouette | Davies-Bouldin | ARI vs sector |
| --- | --- | --- | --- | --- |
| K-means | 4 | 0.385 | 0.748 | 0.070 |
| Hierarchical | 4 | 0.385 | 0.748 | 0.070 |
| K-medoids | 4 | 0.380 | 0.744 | 0.152 |
| DBSCAN | 3 | 0.366 | 0.625 | 0.089 |

Agreement between algorithms is high - 1.00 between k-means and hierarchical,
0.84 with k-medoids - while agreement with sector is near zero throughout.

### The clusters that do form

K-means at k=4 produces groupings that cut cleanly across industry:

| Cluster | Members | Character |
| --- | --- | --- |
| 0 | AMD, NVDA, TSLA, UUUU | Highest return and volatility, beta above 1.7 |
| 1 | AMZN, BP, CVX, INTC, META, NVO, PFE, RTX | Middling returns, deeper drawdowns |
| 2 | AAPL, GOOGL, LLY, MSFT | High Sharpe, moderate beta, shallow drawdowns |
| 3 | AZN, IBM, MRK, WM | Lowest beta and volatility, defensive |

Cluster 2 pairs three technology giants with a pharmaceutical company. Cluster 3
groups a technology firm, a waste management company and two pharmaceutical
firms. Neither is a sector.

### Structure in two dimensions

Principal component analysis captures 93% of the variance in two interpretable
components.

**PC1 (66.7%)** is market risk exposure - return, volatility and beta load
positively, drawdown negatively.

**PC2 (26.3%)** is risk-adjusted quality - Sharpe ratio and drawdown load at
0.66, beta near zero. High values mean efficient returns without severe losses,
independent of market exposure.

Sector membership predicts position on neither axis.

### Where sector does carry information

The timing of maximum drawdowns clusters by industry. Industrials bottomed in
March 2020, technology and semiconductors in late 2022, and every pharmaceutical
stock except AZN in 2025 or 2026. Sector appears to influence when a stock
falls, even where it fails to predict how much risk it carries.

## Sector definitions

Groupings are hand-assigned rather than drawn from a standard classification
such as GICS, and some are deliberately loose.

| Group | Tickers |
| --- | --- |
| Big Tech | AAPL, AMZN, META, GOOGL, MSFT |
| Chips and EV | IBM, TSLA, NVDA, AMD, INTC |
| Pharma | LLY, MRK, NVO, AZN, PFE |
| Industrial | CVX, WM, BP, UUUU, RTX |

Within-sector variance analysis suggests the looseness is not the problem.
Comparing within-sector spread against overall spread, only Big Tech is
meaningfully tight at 0.45. Chips and EV scores 1.07 - more internally varied
than the market as a whole, meaning the label carries negative information.

## Limitations

Twenty stocks is a small sample, and one unusual holding exerts substantial
influence. The sector labels are hand-assigned. The five features are
correlated, with Sharpe ratio derived from two of the others. The ten year
window includes COVID, though only 6 of 20 stocks recorded their worst drawdown
in 2020.

## Repository Contents

```
StockRiskClustering/
├── Stock Clustering.ipynb
├── .gitignore
├── README.md
├── LICENSE
└── requirements.txt
```

## Setup

```bash
pip install -r requirements.txt
jupyter notebook "Stock Clustering.ipynb"
```

Data is downloaded automatically from the Yahoo Finance API. No API key is
required.

## Technologies

Python, pandas, NumPy, scikit-learn, pyclustering, yfinance, matplotlib, seaborn

## Author

David Heffernan | [GitHub](https://github.com/DavidHeffernan94) | [LinkedIn](https://www.linkedin.com/in/david-heffernan-871a85192/)

## License

MIT
