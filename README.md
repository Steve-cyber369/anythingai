# Trader Behavior Insights — Fear & Greed Analysis
**Round-0 Submission | Python Development Intern @ Anything.ai**

---

## 🧠 Why This Analysis?

Crypto markets are heavily driven by **human emotion**. The Fear & Greed Index
captures that emotion daily — scoring market sentiment from *Extreme Fear* to
*Extreme Greed* based on volatility, momentum, social media, and dominance signals.

The core hypothesis driving this project:

> *"If market sentiment influences trader psychology, it should leave a measurable
> fingerprint on trade outcomes — win rates, PnL, position behavior, and timing."*

Most traders make decisions based on price alone. This analysis asks a different
question: **does the emotional state of the market predict whether your trade
will succeed or fail?**

---

## 🔗 How We Find the Relationships

### Step 1 — Linking Emotion to Trade Outcomes
We merged two independent datasets on **date** as the common key:

```
Fear & Greed Index  ──(date)──▶  Hyperliquid Trade Records
      ↓                                    ↓
Daily sentiment label              Individual trade PnL,
(Extreme Fear → Extreme Greed)     side, size, account, coin
```

Every trade now carries a sentiment label for the day it was executed. This
lets us ask: *"Of all trades made on Extreme Greed days, what was the average
outcome?"*

### Step 2 — Slicing the Data Multiple Ways
We don't just look at PnL — we look at the same question from 6 angles:

| Analysis | Question Asked |
|---|---|
| Mean & Median PnL | Does sentiment shift average profitability? |
| Win Rate | Does sentiment change the probability of a winning trade? |
| BUY vs SELL split | Does sentiment affect whether to go long or short? |
| Contrarian strategy | Does trading *against* sentiment pay off? |
| Position size | Do larger trades perform differently across sentiments? |
| Time of day | Are there intraday patterns within sentiment periods? |

### Step 3 — Statistical Validation
We don't just eyeball charts. We use the **Kruskal-Wallis test** — a
non-parametric test that checks whether PnL distributions across sentiment
groups are statistically different or just random noise.

- H = 677.21, p ≈ 0.000
- This means: **the differences are real, not luck**

### Step 4 — Trader-Level Lens
We compute a **Sharpe-like consistency score** (avg_pnl / std_pnl) per trader
to separate lucky one-hit traders from genuinely skilled consistent performers.

---

## 📂 Dataset

| File | Rows | Description |
|---|---|---|
| `fear_greed_index.csv` | 2,644 | Daily sentiment: date, value, classification |
| `historical_data.csv` | 211,224 | Trade records: account, coin, side, PnL, size, timestamp |

**Source:** Fear & Greed Index (alternative.me) + Hyperliquid DEX trade history

---

## 📈 Key Findings

### 1. Sentiment Strongly Predicts PnL

| Sentiment | Mean PnL | Median PnL | Win Rate | Trades |
|---|---|---|---|---|
| Extreme Fear | $1.89 | $0.00 | 29.3% | 2,326 |
| Fear | $128.29 | $0.00 | 38.2% | 13,869 |
| Neutral | $27.09 | $0.00 | 49.5% | 2,756 |
| Greed | $53.99 | $0.00 | 43.6% | 11,292 |
| Extreme Greed | $205.82 | $0.96 | 55.3% | 5,621 |

**Finding:** Extreme Greed periods produce 108x higher mean PnL than Extreme
Fear. Win rate nearly doubles (29% → 55%) from worst to best sentiment.

### 2. The Median is Always $0 — What That Means

Across every sentiment period, the median PnL is $0. This reveals a
**highly skewed distribution** — a small number of very large winning trades
pull the mean up, while the majority of trades break even or lose slightly.
The market is dominated by a few skilled (or lucky) outliers.

### 3. SELL Trades Dominate in 4/5 Conditions

| Sentiment | BUY Avg | SELL Avg | Winner |
|---|---|---|---|
| Extreme Fear | -$3.25 | +$7.08 | SELL ✅ |
| Fear | +$210.43 | +$36.82 | BUY ✅ |
| Neutral | +$12.48 | +$35.67 | SELL ✅ |
| Greed | +$15.66 | +$89.20 | SELL ✅ |
| Extreme Greed | +$9.05 | +$288.35 | SELL ✅ |

**Finding:** SELL (short) positions outperform BUY in 4 out of 5 sentiment
conditions. The only exception is Fear — where buying the dip works.
During Extreme Greed, SELL trades average 31x more than BUY trades.

### 4. Contrarian Strategy Result
Trading *against* the crowd (BUY during Fear, SELL during Greed) was tested
as a systematic strategy against random direction trading.

### 5. Top Trader Insight
- Top trader: **$2,143,382 total PnL** across 14,733 trades at just 34% win rate
- This proves the key insight: **win rate alone doesn't determine profitability**
  — position sizing and loss management matter more.
- Best consistency score traders maintained positive returns across all
  sentiment conditions, not just one.

### 6. Statistical Significance
Kruskal-Wallis test: **H = 677.21, p ≈ 0.000**
The null hypothesis (sentiment has no effect on PnL) is rejected with
near-zero probability of error. Sentiment is a genuine signal, not noise.

---

## 💡 Actionable Insights for Traders

1. **Follow Extreme Greed with SELL bias** — highest PnL period, SELL dominates
2. **Buy the dip during Fear** — the only condition where BUY outperforms SELL
3. **Avoid trading during Extreme Fear** — 29% win rate means 7/10 trades lose
4. **Don't chase win rate** — top trader wins only 34% of the time but manages
   losses tightly and lets winners run
5. **Neutral sentiment = balanced** — closest to 50/50, least edge in either direction

---

## 🛠️ Tech Stack

| Tool | Purpose |
|---|---|
| Python 3.10 | Core language |
| pandas | Data loading, cleaning, merging, groupby analysis |
| numpy | Numerical operations |
| matplotlib | Base charting — bar, line, pie, scatter |
| seaborn | Statistical charts — boxplot, heatmap |
| scipy.stats | Kruskal-Wallis significance test |
| Groq (Llama 3.3 70B) | LLM reasoning layer for trading strategy insights |
| Google Colab | Execution environment |

---

## 🚀 How to Run

### Option 1 — Google Colab (Recommended)
1. Open [Google Colab](https://colab.research.google.com)
2. Upload `trader_analysis_groq.ipynb`
3. Upload both CSV files to Google Drive
4. Add your Groq API key in Cell 1 (free at [console.groq.com](https://console.groq.com))
5. Runtime → Run All

### Option 2 — Local
```bash
pip install pandas numpy matplotlib seaborn scipy groq openpyxl
jupyter notebook trader_analysis_groq.ipynb
```

### File paths to update in Cell 1:
```python
FG_PATH     = '/content/drive/MyDrive/fear_greed_index.csv'
TRADES_PATH = '/content/drive/MyDrive/historical_data.csv'
GROQ_API_KEY = 'your_key_here'
```

---

## 📁 Output Files

```
outputs/
├── full_dashboard.png       # 8-panel analysis dashboard
├── cumulative_pnl.png       # PnL timeline with sentiment overlay
└── trader_leaderboard.png   # Top & bottom 10 traders
```

---

## 📓 Notebook Structure

| Cell | Contents |
|---|---|
| 1 | Mount Drive, install Groq, setup LLM function |
| 2 | Load both datasets |
| 3 | Clean data, parse timestamps, rename columns |
| 4 | Merge on date, create derived columns |
| 5 | Core analysis — PnL, win rate, BUY/SELL, stats test |
| 6 | Extended analysis — contrarian, consistency, hourly, coins, streaks |
| 7 | Groq LLM generates 4 expert insights from real data |
| 8 | Full 8-chart dashboard |
| 9 | Cumulative PnL timeline |
| 10 | Trader leaderboard |
| 11 | Groq auto-generates README draft |
| 12 | Final formatted summary report |

---

*Submitted for Round-0 screening — Python Development Intern @ Anything.ai*
