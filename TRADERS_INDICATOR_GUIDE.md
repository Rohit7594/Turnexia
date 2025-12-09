# 📈 AKS Market Screener - Trader's Indicator Guide

> **Understanding every metric on your screen**

---

## Quick Reference Card

| Indicator | What It Tells You | Good Sign | Warning Sign |
|-----------|-------------------|-----------|--------------|
| **Volume % Change** | Is the market more or less active than usual? | > +20% (high interest) | < -20% (low conviction) |
| **MCAP Weighted** | Are big stocks or small stocks more active? | MCAP ≈ Regular (broad) | MCAP << Regular (retail-driven) |
| **Volume Breadth** | How many stocks are seeing action? | > 50% (broad participation) | < 30% (narrow, concentrated) |
| **Flow Direction** | Is money flowing into winners or losers? | 🟢 BUYING | 🔴 SELLING |
| **Turnover** | Total rupee value traded | Higher = more conviction | Lower = thin market |

---

## 📊 The Stock Table

### Price Columns

#### LAST CLOSE
**What it is**: Yesterday's closing price  
**Timeframe**: Previous trading day's 3:30 PM value  
**Use it to**: Compare with today's open for gap analysis

#### OPEN
**What it is**: Today's opening price at 9:15 AM  
**Use it to**: 
- Gap up (Open > Last Close) = Bullish overnight sentiment
- Gap down (Open < Last Close) = Bearish overnight sentiment

#### CURRENT
**What it is**: Live trading price (updates every few seconds during market hours)  
**After hours**: Shows last traded price from 3:30 PM

#### 1D CHANGE / 1D CHANGE %
**What it is**: How much the stock moved today
```
1D Change = Current Price - Last Close
1D Change % = (1D Change / Last Close) × 100
```
**Display**:
- ▲ Green = Stock is UP
- ▼ Red = Stock is DOWN

#### N-Day PRICE / N-Day CHANGE %
**What it is**: Comparison with price from N trading days ago  
**Default**: 10 days (you can change this in the "Days for Comparison" input)

**Example** (10-day comparison):
```
Today's price: ₹2,850
10 days ago: ₹2,700
10D Change = ₹150
10D Change % = +5.56%
```

**Use it to**: 
- Identify short-term trends
- Set it to 50 or 200 for medium/long-term analysis

---

### Volume Columns

#### AVG VOLUME
**What it is**: Average daily trading volume over the **last 30 trading days**  
**Calculation**: Simple arithmetic mean of 30 daily volumes  
**Unit**: Number of shares

#### TODAY VOLUME
**What it is**: Total shares traded today so far  
**During market hours**: Cumulative volume from 9:15 AM until now  
**After market**: Full day's volume

#### VOL CHANGE %
**What it is**: How today's volume compares to the 30-day average
```
Vol Change % = ((Today Volume - Avg Volume) / Avg Volume) × 100
```

**Interpretation**:
| Value | Meaning | Trading Implication |
|-------|---------|---------------------|
| **+50%** | 50% more volume than usual | High interest, breakout candidate |
| **+20%** | Moderately higher volume | Above normal activity |
| **0%** | Normal volume | Regular trading day |
| **-30%** | 30% less volume | Low interest, avoid |
| **-50%** | Half the normal volume | Very thin, wide spreads |

---

### Fundamental Columns

#### MARKET CAP (Cr)
**What it is**: Total value of all company shares  
**Calculation**: `Issued Shares × Current Price`  
**Unit**: Crores (₹1 Crore = ₹10,000,000)

**Categories**:
| Market Cap | Category |
|------------|----------|
| > ₹1,00,000 Cr | Mega Cap |
| ₹20,000 - ₹1,00,000 Cr | Large Cap |
| ₹5,000 - ₹20,000 Cr | Mid Cap |
| < ₹5,000 Cr | Small Cap |

#### P/E (Price-to-Earnings)
**What it is**: How much you pay for ₹1 of company earnings  
**Calculation**: `Stock Price / Earnings Per Share`

**Interpretation**:
- Low P/E (< 15): Possibly undervalued or slow growth
- Moderate P/E (15-25): Fair value range
- High P/E (> 25): Growth stock or overvalued

**Compare**: Always compare P/E within the same sector

#### EPS (Earnings Per Share)
**What it is**: Profit earned per share  
**Calculation**: `Stock Price / P/E Ratio`  
**Unit**: ₹ per share

#### 52W HIGH / 52W LOW
**What it is**: Highest and lowest prices in the last 52 weeks (1 year)

**Use it to**:
- Check if stock is near breakout (close to 52W High)
- Check if stock is at support (near 52W Low)
- Calculate distance from high: `((Current - 52W High) / 52W High) × 100`

---

## 📊 Aggregate Volume Indicator Card

This is the **main dashboard widget** at the top showing market-wide volume analysis.

### 1️⃣ VOLUME % CHANGE (Main Number)

**The Big Number You See First**

```
         +12.5%
    vs 30-day average
```

**What it measures**: Total volume across ALL stocks compared to their combined 30-day average

**Calculation**:
```
1. Add up today's volume for all stocks
2. Add up average volume for all stocks
3. Volume Ratio = Total Today / Total Average
4. Volume % Change = (Ratio - 1) × 100
```

**Alert Levels**:

| Volume Ratio | Alert | What It Means |
|--------------|-------|---------------|
| ≥ 2.0× (≥+100%) | 🔥 EXTREME VOLUME | Major news event, be cautious |
| 1.5× - 2.0× (+50% to +100%) | 📈 VERY HIGH | Strong institutional activity |
| 1.2× - 1.5× (+20% to +50%) | ↗️ ELEVATED | Above normal interest |
| 0.8× - 1.2× (-20% to +20%) | ➡️ NORMAL | Regular market conditions |
| 0.5× - 0.8× (-50% to -20%) | ↘️ LOW | Low conviction, be careful |
| < 0.5× (< -50%) | 📉 VERY LOW | Avoid trading, thin liquidity |

---

### 2️⃣ TURNOVER TODAY

**What it is**: Total rupee value of all shares traded today

```
₹42.22K Cr
Avg: ₹37.54K Cr
```

**Data Source**: Yahoo Finance (Close Price × Volume)

**Calculation**: `Sum of (Close Price × Volume)` for all stocks from Yahoo Finance

> **📌 CONSISTENCY NOTE**: TURNOVER TODAY uses the **same data source** as the Weekly Turnover Chart (Yahoo Finance). This ensures that today's turnover value will **exactly match** the corresponding bar in the weekly chart.

**Why it matters**: 
- Volume alone doesn't tell the full story
- 1 million shares of a ₹2,000 stock = ₹200 Cr
- 1 million shares of a ₹20 stock = ₹2 Cr
- Turnover shows actual money flowing

---

### 3️⃣ MCAP WEIGHTED

**What it is**: Volume change weighted by company size

```
MCAP Weighted: +4.4%
Large caps leading/lagging
```

**Calculation**:
```
For each stock:
    Contribution = Market Cap × Volume Change %

MCAP Weighted = Sum of Contributions / Total Market Cap
```

**How to interpret**:

| Comparison | Meaning | Implication |
|------------|---------|-------------|
| **MCAP > Regular** | Large caps (Reliance, HDFC, TCS) are more active | Institutional buying - likely sustainable |
| **MCAP < Regular** | Small/mid caps are more active | Retail-driven - be cautious |
| **MCAP ≈ Regular** | Activity is spread across all sizes | Broad-based move, high conviction |

**Example**:
```
Volume % Change: +12.5%
MCAP Weighted:    +4.4%

Gap = 8.1% → Small caps are driving the volume
```

---

### 4️⃣ VOLUME BREADTH

**What it is**: Percentage of stocks with significantly above-average volume

```
41% (41/101)
[========------] above avg volume
```

**Calculation**:
- **Elevated**: Volume > 1.2× average (20%+ above)
- **Normal**: Volume between 0.8× and 1.2× average
- **Reduced**: Volume < 0.8× average (20%+ below)

**How to interpret**:

| Breadth | Market Condition |
|---------|------------------|
| **> 70%** | ✅ Broad participation - strong trend |
| **50-70%** | ⚡ Mixed - some sectors leading |
| **< 50%** | ⚠️ Narrow - concentrated in few stocks |
| **< 30%** | 🚨 Very narrow - news/event driven |

**Trading insight**:
- High volume with **high breadth** = Strong institutional buying
- High volume with **low breadth** = Concentrated in few stocks (less reliable)

---

### 5️⃣ FLOW DIRECTION

**What it is**: Is money flowing into stocks going UP or DOWN?

```
🟢 BUYING
↑45 ↓52 →3
```

**Calculation**:
```
Up Turnover = Sum of turnover for stocks with positive price change
Down Turnover = Sum of turnover for stocks with negative price change

Flow Ratio = (Up Turnover / Total Turnover) × 100
```

**Thresholds**:

| Flow Ratio | Status | Meaning |
|------------|--------|---------|
| ≥ 60% | 🟢 STRONG BUYING | Most money in rising stocks |
| 52-60% | 🟢 BUYING | Slight bullish tilt |
| 48-52% | ⚪ NEUTRAL | Balanced flow |
| 40-48% | 🔴 SELLING | Slight bearish tilt |
| < 40% | 🔴 STRONG SELLING | Most money in falling stocks |

**The small text**: `↑45 ↓52 →3` means:
- 45 stocks are UP today
- 52 stocks are DOWN today
- 3 stocks are FLAT (unchanged)

---

### 6️⃣ INTRADAY PROJECTION (During Market Hours Only)

**What it is**: Projected full-day volume based on current pace

```
⏱️ Intraday: 50% of trading day
Projected: +65%
```

**Important**: Volume is **NOT linear** throughout the day!

**Actual market volume pattern**:
| Time | Expected Volume Done |
|------|---------------------|
| 9:30 AM | ~5% |
| 10:00 AM | ~15% |
| 11:00 AM | ~31% |
| 12:00 PM | ~44% (midday lull) |
| 2:00 PM | ~69% |
| 3:00 PM | ~91% (closing rush) |
| 3:30 PM | 100% |

**The U-Shape**: High volume at open, low at midday, high at close

**How projection works**:
```
At 10:30 AM, expected = 23%
If actual volume = 20%

Projected full-day = 20% / 23% × 100 = 87% of average
Projected change = -13%
```

---

## 📊 Weekly Turnover Chart

**What it shows**: Bar chart of total market turnover for the last 7 trading days

**Data Source**: Yahoo Finance (same as TURNOVER TODAY)

**Calculation for each day**:
```
Daily Turnover = Sum of (Close Price × Volume) for all stocks
```

> **📌 CONSISTENCY**: The weekly chart and TURNOVER TODAY use the **exact same data source and calculation**. The most recent bar in the chart will always match TURNOVER TODAY.

**Use it to**:
- Spot volume trends over the week
- Identify if today is unusually high/low compared to recent days
- Compare Monday vs Friday patterns

---

## 🎯 Trading Scenarios

### Scenario 1: Breakout Confirmation ✅
```
Volume % Change: +50%
MCAP Weighted: +45%
Volume Breadth: 75%
Flow: 🟢 STRONG BUYING
```
**Interpretation**: High volume, large caps leading, broad participation, money in winners
**Action**: High conviction entry on breakouts

---

### Scenario 2: False Breakout Warning ⚠️
```
Volume % Change: +40%
MCAP Weighted: +5%
Volume Breadth: 35%
Flow: 🟢 BUYING
```
**Interpretation**: High volume BUT driven by small caps, narrow participation
**Action**: Be cautious, wait for confirmation

---

### Scenario 3: Broad Selloff 🔴
```
Volume % Change: +25%
MCAP Weighted: +30%
Volume Breadth: 60%
Flow: 🔴 STRONG SELLING
```
**Interpretation**: Above-average volume, large caps active, but money in falling stocks
**Action**: Defensive mode, tighten stops, avoid new longs

---

### Scenario 4: Low Conviction Day 😐
```
Volume % Change: -30%
MCAP Weighted: -25%
Volume Breadth: 25%
Flow: ⚪ NEUTRAL
```
**Interpretation**: Below-average volume, narrow participation
**Action**: Reduce position sizes, wider spreads expected

---

## ⏰ Data Refresh

| What | Update Frequency |
|------|------------------|
| Stock prices | Real-time during market (9:15 AM - 3:30 PM) |
| Volume | Real-time (aggregated minute-by-minute) |
| All indicators | Auto-refresh every 5 minutes |
| Manual refresh | Click "⟳ Refresh Data" button |

---

## 🔑 Key Takeaways for Traders

1. **Always check Volume % Change first** - Low volume = low conviction moves

2. **Compare MCAP Weighted vs Regular** 
   - MCAP > Regular = Institutions active (follow)
   - MCAP < Regular = Retail active (be cautious)

3. **Volume Breadth > 50% is healthy** - Broad participation = sustainable move

4. **Flow Direction confirms price action**
   - Price up + 🟢 BUYING = Strong trend
   - Price up + 🔴 SELLING = Potential reversal

5. **Intraday projection accounts for the U-shape** - Morning volume is typically higher

6. **Use multiple indicators together** - No single metric tells the full story

---

*Last updated: December 2025*
