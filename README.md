# Trader Performance vs Bitcoin Market Sentiment Analysis

![Python](https://img.shields.io/badge/Python-3.11-blue)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-green)
![Status](https://img.shields.io/badge/Status-Completed-success)

## 📌 Project Overview
This project investigates how **Bitcoin market sentiment** (Fear & Greed Index) relates to **trader performance** on **Hyperliquid**. Individual trade-level execution data is merged with daily sentiment classifications to uncover patterns in profitability, risk-taking, trading volume, and trader concentration — with the goal of informing smarter, sentiment-aware trading strategies.

Submitted as part of the **PrimeTrade.ai Data Science Hiring Assignment**.

---

## 📂 Datasets Used

### 1. Bitcoin Fear & Greed Index (`fear_greed_index.csv`)
| Column | Description |
|---|---|
| `timestamp` | Unix timestamp of the record |
| `value` | Fear/Greed numeric score (0–100) |
| `classification` | Sentiment label (Extreme Fear, Fear, Neutral, Greed, Extreme Greed) |
| `date` | Calendar date |

- **Rows:** 2,644

### 2. Historical Trader Data — Hyperliquid (`historical_data.csv`)
| Column | Description |
|---|---|
| `Account` | Trader's wallet/account identifier |
| `Coin` | Traded asset (e.g., BTC, ETH, HYPE) |
| `Execution Price` | Price at which trade executed |
| `Size Tokens` | Trade size in tokens |
| `Size USD` | Trade size in USD |
| `Side` | Buy/Sell |
| `Timestamp IST` | Trade execution time (IST) |
| `Start Position` | Position size before trade |
| `Direction` | Trade direction (Open Long, Close Short, etc.) |
| `Closed PnL` | Realized profit/loss on the trade |
| `Transaction Hash` | Blockchain transaction ID |
| `Order ID` | Unique order identifier |
| `Crossed` | Whether the order crossed the spread |
| `Fee` | Trading fee paid |
| `Trade ID` | Unique trade identifier |
| `Timestamp` | Unix timestamp of trade |

- **Rows:** 23,285

---

## 🛠️ Project Workflow

### 1. Import Libraries
`pandas`, `numpy`, `matplotlib`, `seaborn` — with display settings configured and warnings suppressed for clean notebook output.

### 2. Load Datasets
Both CSVs loaded and inspected with `.info()` / `.head()` to validate schema, row counts, and null distribution before any transformation.

### 3. Data Cleaning
- Parsed `Timestamp IST` into proper datetime and derived a clean `date` key for merging.
- Standardized the Fear & Greed `date` column to the same format.
- Handled minor nulls in `Order ID`, `Crossed`, `Fee`, `Trade ID`, `Timestamp`.
- Removed duplicate records.
- Verified date-range overlap between both datasets before merge.

### 4. Merge Datasets
Merged on the common `date` field so every individual trade carries the corresponding day's sentiment classification and Fear/Greed score.

### 5. Exploratory Data Analysis (EDA)
Analysis covered: PnL by sentiment, win rate by sentiment, trade size by sentiment, buy/sell mix, most-traded coins, account-level profitability, daily aggregated PnL trend, and cross-feature correlations.

### 6. Charts & Visualizations
All plots saved to `/plots`:

| Chart | Key Observation |
|---|---|
| **Fear & Greed Classification** (trade-day frequency) | Fear (~10.9K) and Greed (~9.9K) days dominate; Extreme Fear is rare (~1.5K), showing the market spent little time at sentiment extremes. |
| **Buy vs Sell Trades** | Near-even split (~18.7K Buy vs ~18.4K Sell) — no strong directional bias overall. |
| **Top 10 Most Traded Coins** | BTC and ETH lead by trade frequency, with HYPE, SOL and a few mid-cap tokens making up the long tail. (Note: two versions of this chart exist in `/plots` with different counts — worth confirming which reflects the final cleaned dataset before including in the report.) |
| **Profit by Classification** | PnL outliers are largest in absolute terms during **Fear** (up to ~+135K) and the single worst loss (~-120K) occurred during **Greed** — extreme sentiment days produce fatter tails in both directions. |
| **Profit/Loss Distribution** | Heavily concentrated near zero with a long right/left tail — most trades are small in PnL impact, a few are very large (classic fat-tailed distribution). |
| **Daily Profit (time series)** | Volatility in daily aggregate PnL increases sharply from ~Feb 2025 onward, with spikes exceeding +500K and drawdowns near -170K — activity/risk clearly intensified in the later period. |
| **Correlation Heatmap** | `Closed PnL` shows weak correlation with all other numeric features (max ~0.13 with Size USD), suggesting PnL isn't simply explained by trade size or fees alone. `Size USD` and `Fee` are strongly correlated (0.72), as expected. `Sentiment value` is negatively correlated with `Order ID`/`Timestamp` (-0.6 / -0.46), reflecting how sentiment trended over the data's time period rather than a behavioral link. |
| **Top 10 Profitable Traders** | Extremely concentrated: the top trader alone banked ~1.6M in profit — more than the next 2 traders combined, and over 20x the 10th-ranked trader (~60K). Classic power-law/Pareto distribution of profits. |

### 7. Key Insights
1. **Fear and Greed days dominate market activity** — the sentiment index rarely sits at Extreme Fear, meaning most trading happens under moderate sentiment conditions, not panic or euphoria.
2. **BTC and ETH remain the core traded assets**, though newer tokens (HYPE, SOL) show meaningful volume — indicating diversification beyond majors.
3. **Trading volume is close to balanced between Buy and Sell**, suggesting no systemic directional bias in this trader cohort.
4. **PnL is fat-tailed, not sentiment-uniform** — the biggest gains cluster in Fear periods and the biggest single loss occurred in a Greed period, hinting that extreme moves (in either direction) tend to coincide with elevated sentiment volatility rather than one sentiment being "safer."
5. **Profitability is highly concentrated** — a small number of accounts (led by one dominant trader at ~1.6M) account for a disproportionate share of total profit, a Pareto-style distribution common in trading populations.
6. **Risk and volatility intensified significantly in 2025**, with daily PnL swings growing much larger from February 2025 onward compared to the relatively calm 2024 period.
7. **Closed PnL is not strongly explained by any single numeric feature** (size, fee, position) — correlations are all weak, implying that trader skill/timing/strategy — not just position size — drives outcomes.
8. **No major data quality issues** remained after cleaning; the dataset merged cleanly on date with minimal information loss.

### 8. Conclusion
Combining Hyperliquid trade-level data with the Bitcoin Fear & Greed Index reveals that market sentiment shapes the *shape* of trading outcomes more than it shapes simple averages: extreme profits and losses cluster around Fear and Greed days respectively, overall trading activity is fairly balanced day-to-day, and a small set of traders captures most of the profit pool. Risk and volatility have also grown substantially in the most recent period covered by the data. These patterns suggest that sentiment-aware position sizing and risk controls — tighter risk limits in high-volatility sentiment regimes, and closer study of the top-performing trader cohort — could meaningfully improve strategy design and risk management.

---

## 💡 Business/Trading Implications
- **Risk management**: Since PnL tails are fattest during Fear and Greed (not Neutral), risk limits could be tightened specifically around sentiment extremes rather than applying a flat risk policy.
- **Strategy design**: The heavy concentration of profit in a handful of accounts suggests studying/emulating top-trader behavior (position timing, coin selection) could be more valuable than broad-based strategy tweaks.
- **Volatility regime awareness**: The sharp rise in daily PnL swings from 2025 onward signals a regime shift — strategies tuned on 2024 data alone may underestimate current risk.
- **Diversification signal**: Emerging tokens (HYPE, SOL) capturing meaningful trade share alongside BTC/ETH suggests sentiment-driven strategies shouldn't be BTC/ETH-only.

---

## 🧰 Tech Stack
- **Language:** Python 3
- **Libraries:** pandas, numpy, matplotlib, seaborn
- **Environment:** Jupyter Notebook

---

## 📁 Repository Structure
```
├── Data/
│   ├── fear_greed_index.csv       # Bitcoin Fear & Greed Index data
│   └── historical_data.csv        # Hyperliquid trading data
├── plots/                         # All exported chart images
├── notebook.ipynb                 # Main analysis notebook
├── README.md                      # Project documentation (this file)
├── report.pdf                     # Exported PDF report
└── requirements.txt                # Python dependencies
```

---

## 🚀 How to Run
1. Clone/download this repository.
2. Keep the folder structure intact (`Data/` and `plots/` alongside the notebook).
3. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
4. Open `notebook.ipynb` in Jupyter Notebook and run all cells sequentially.

---

## 👤 Author
**Ritesh**

- B.Tech CSE, Mangalmay Institute of Management & Technology (AKTU)
- GitHub: https://github.com/RIT8603
- LinkedIn: https://linkedin.com/in/ritesh-nxt
- Portfolio: https://riteshnxt.dev

---

*Submitted as part of the PrimeTrade.ai Data Science hiring assignment.*
