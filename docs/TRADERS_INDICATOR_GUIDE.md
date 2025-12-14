# 📈 Turnexia Stock Screener - Trader's Indicator Guide

> **Understanding every metric on your screen**

---

## Quick Reference Card

| Indicator | What It Tells You | Good Sign | Warning Sign |
|-----------|-------------------|-----------|--------------|
| **Volume % Change** | Market activity vs average | > +20% (high interest) | < -20% (low conviction) |
| **MCAP Weighted** | Big vs small stock activity | MCAP ≈ Regular (broad) | MCAP << Regular (retail) |
| **Volume Breadth** | Stocks seeing action | > 50% (broad) | < 30% (narrow) |
| **Flow Direction** | Money into winners/losers | 🟢 BUYING | 🔴 SELLING |
| **Turnover** | Total ₹ value traded | Higher = conviction | Lower = thin market |

---

## 📈 Index Candlestick Chart

### What It Shows
- **60-day daily candlesticks** for selected index (NIFTY 50 or NIFTY 100)
- **20-day EMA** (dotted yellow line) for trend identification

### How to Read
| Pattern | EMA Position | Signal |
|---------|--------------|--------|
| Price above EMA | Uptrend | Bullish continuation |
| Price below EMA | Downtrend | Bearish caution |
| Price crossing EMA | Trend change | Watch for confirmation |

### The Header Shows
- Index name with **60-day % change** (▲ green for up, ▼ red for down)
- Current price annotation on the chart

---

## 📊 The Stock Table

### Price Columns

| Column | Description | Use It To |
|--------|-------------|-----------|
| **LAST CLOSE** | Yesterday's 3:30 PM price | Compare with today's open for gaps |
| **OPEN** | Today's 9:15 AM price | Gap up/down = overnight sentiment |
| **CURRENT** | Live price | Track real-time movement |
| **1D CHANGE %** | Today's movement | ▲ Green = UP, ▼ Red = DOWN |
| **N-Day CHANGE** | Compare to N days ago | Customize via input (10, 50, 200) |

### Volume Columns

| Column | Description | Interpretation |
|--------|-------------|----------------|
| **AVG VOLUME** | 10-day average | Baseline for comparison |
| **TODAY VOLUME** | Shares traded today | Compare to average |
| **VOL CHANGE %** | Volume vs average | +50% = breakout candidate, -30% = avoid |

### Fundamental Columns

| Column | What It Means |
|--------|---------------|
| **MARKET CAP** | Total company value (in Crores) |
| **P/E** | Price ÷ Earnings (< 15 = cheap, > 25 = expensive) |
| **EPS** | Earnings per share |
| **52W HIGH/LOW** | Year range (near high = breakout, near low = support) |

---

## 📊 Aggregate Volume Indicator Card

### 1️⃣ VOLUME % CHANGE (Main Number)

**The Big Number**: Total volume vs 10-day average

| Volume Ratio | Alert | Meaning |
|--------------|-------|---------|
| ≥ 2.0× | 🔥 EXTREME | Major news event |
| 1.5× - 2.0× | 📈 VERY HIGH | Strong institutional activity |
| 1.2× - 1.5× | ↗️ ELEVATED | Above normal |
| 0.8× - 1.2× | ➡️ NORMAL | Regular conditions |
| 0.5× - 0.8× | ↘️ LOW | Low conviction |
| < 0.5× | 📉 VERY LOW | Thin liquidity |

---

### 2️⃣ TURNOVER TODAY

**What**: Total ₹ value traded (Yahoo Finance: Close × Volume)

> **📌 Consistency**: Uses same source as Weekly Turnover Chart

---

### 3️⃣ MCAP WEIGHTED

**What**: Volume change weighted by company size

| Comparison | Meaning | Implication |
|------------|---------|-------------|
| MCAP > Regular | Large caps active | Institutional - sustainable |
| MCAP < Regular | Small caps active | Retail - be cautious |
| MCAP ≈ Regular | Spread across sizes | Broad-based move |

---

### 4️⃣ VOLUME BREADTH

**What**: % of stocks with 20%+ above-average volume

| Breadth | Condition |
|---------|-----------|
| > 70% | ✅ Strong trend |
| 50-70% | ⚡ Mixed |
| < 50% | ⚠️ Narrow |
| < 30% | 🚨 News-driven |

---

### 5️⃣ FLOW DIRECTION

**What**: Money into UP vs DOWN stocks

| Flow Ratio | Status |
|------------|--------|
| ≥ 60% | 🟢 STRONG BUYING |
| 52-60% | 🟢 BUYING |
| 48-52% | ⚪ NEUTRAL |
| 40-48% | 🔴 SELLING |
| < 40% | 🔴 STRONG SELLING |

---

### 6️⃣ INTRADAY PROJECTION

**What**: Projected full-day volume (accounts for U-shape pattern)

| Time | Expected Volume |
|------|-----------------|
| 10:00 AM | ~15% |
| 12:00 PM | ~44% (midday lull) |
| 3:00 PM | ~91% (closing rush) |

---

## 📊 Weekly Turnover Chart

**What**: 7-day bar chart of market turnover (Yahoo Finance data)

**Use it to**:
- Spot weekly volume trends
- Identify unusual days
- Compare Monday vs Friday patterns

---

## 🎯 Trading Scenarios

### ✅ Breakout Confirmation
| Metric | Value |
|--------|-------|
| Volume | +50% |
| MCAP Weighted | +45% |
| Breadth | 75% |
| Flow | 🟢 STRONG BUYING |

**Action**: High conviction entry

### ⚠️ False Breakout Warning
| Metric | Value |
|--------|-------|
| Volume | +40% |
| MCAP Weighted | +5% |
| Breadth | 35% |
| Flow | 🟢 BUYING |

**Action**: Wait for confirmation

### 🔴 Broad Selloff
| Metric | Value |
|--------|-------|
| Volume | +25% |
| MCAP Weighted | +30% |
| Breadth | 60% |
| Flow | 🔴 STRONG SELLING |

**Action**: Defensive mode

### 😐 Low Conviction
| Metric | Value |
|--------|-------|
| Volume | -30% |
| MCAP Weighted | -25% |
| Breadth | 25% |
| Flow | ⚪ NEUTRAL |

**Action**: Reduce position sizes

---

## 🔑 Key Takeaways

1. **Volume % Change first** - Low volume = low conviction
2. **MCAP vs Regular** - MCAP > Regular = institutions (follow)
3. **Breadth > 50%** - Broad participation = sustainable
4. **Flow confirms price** - Price up + 🔴 SELLING = reversal risk
5. **Use multiple indicators** - No single metric tells full story

---

*Last updated: December 2025*
