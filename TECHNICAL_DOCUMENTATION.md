# 📊 Stock Screener - Technical Documentation

> **Complete guide to data sources, calculations, indicators, and technology**

---

## Table of Contents

1. [Technology Stack](#technology-stack)
2. [Data Sources](#data-sources)
3. [Data Processing Pipeline](#data-processing-pipeline)
4. [Individual Stock Metrics](#individual-stock-metrics)
5. [Aggregate Volume Indicator](#aggregate-volume-indicator)
6. [Weekly Turnover Chart](#weekly-turnover-chart)
7. [Caching Strategy](#caching-strategy)
8. [Market Hours & Timezone](#market-hours--timezone)

---

## Technology Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Frontend Framework** | Dash (Plotly) | Interactive web UI with callbacks |
| **UI Components** | dash-bootstrap-components | Bootstrap-styled components (cards, tables) |
| **Charting** | Plotly Graph Objects | Interactive bar charts, visualizations |
| **Backend** | Python 3.x | Data processing, API calls |
| **Data Processing** | Pandas | DataFrame operations, statistics |
| **NSE Data API** | nsepython | Real-time NSE stock data |
| **Historical Data API** | yfinance | Yahoo Finance historical OHLCV data |
| **Caching** | cachetools (TTLCache) | 5-minute TTL cache for API responses |
| **Timezone** | pytz | IST timezone-aware calculations |
| **Logging** | Python logging | Structured application logs |

### File Structure

```
stock_screener/
├── app_optimized.py          # Main application (1840 lines)
├── nifty100_with_industries.csv  # Static symbol-industry mapping
├── requirements.txt          # Dependencies
├── INDICATOR_GUIDE.md        # User-facing indicator guide
└── venv/                     # Python virtual environment
```

---

## Data Sources

### 1. NSE Python API (`nsepython`)

**Endpoint**: `nse_eq(symbol)` function

**Data Retrieved**:
| Field | NSE JSON Path | Description |
|-------|--------------|-------------|
| Last Price | `priceInfo.lastPrice` | Current trading price |
| Previous Close | `priceInfo.previousClose` | Yesterday's closing price |
| Open Price | `priceInfo.open` | Today's opening price |
| P/E Ratio | `metadata.pdSymbolPe` | Price-to-Earnings ratio |
| 52W High | `priceInfo.weekHighLow.max` | 52-week high price |
| 52W Low | `priceInfo.weekHighLow.min` | 52-week low price |
| Issued Shares | `securityInfo.issuedSize` | Total issued shares |
| Sector | `industryInfo.industry` | Industry classification |

**Resolution**: Real-time (live market data during trading hours)

**Rate Limiting**: Built-in retry with exponential backoff (0.5s, 1s, 2s)

---

### 2. Yahoo Finance API (`yfinance`)

**Ticker Format**: `{SYMBOL}.NS` (e.g., `RELIANCE.NS`)

#### Historical Data (30-day)

```python
tk.history(period="30d", interval="1d")
```

**Columns Retrieved**: Open, High, Low, Close, Volume, Dividends, Stock Splits

**Used For**:
- 30-day average volume calculation
- Weekly turnover data (last 7 days)
- N-day historical price comparison

#### Volume Data

**30-Day Average Volume**:
```python
hist_30 = tk.history(period="30d", interval="1d")
avg_vol = float(hist_30["Volume"].mean())
```

**Today's Volume** (two methods, with fallback):

```python
# Method 1: Intraday 1-minute data
intraday = tk.history(period="1d", interval="1m")
todays_vol = float(intraday["Volume"].sum())

# Method 2: Daily data fallback
daily = tk.history(period="1d", interval="1d")
todays_vol = float(daily["Volume"].iloc[-1])
```

**Resolution**:
- Historical: Daily OHLCV (1-day interval)
- Today's Volume: 1-minute aggregated OR daily

---

### 3. Static CSV Data

**File**: `nifty100_with_industries.csv`

**Format**:
```csv
ticker,symbol,industry
NSE:RELIANCE,RELIANCE,Petroleum Products
NSE:TCS,TCS,IT - Software
NSE:HDFCBANK,HDFCBANK,Banks
...
```

**Purpose**: Pre-loaded symbol-to-industry mapping for fast filtering

**Total Symbols**: 102 Nifty 100 stocks

---

## Data Processing Pipeline

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                        DATA FLOW ARCHITECTURE                               │
└─────────────────────────────────────────────────────────────────────────────┘

1. USER selects industry from dropdown
              ↓
2. Callback triggers fetch_stocks_data_for_industry()
              ↓
3. ThreadPoolExecutor (5 workers) fetches data in parallel
              ↓
   ┌──────────────────────────────────────────────────────────────────────┐
   │  For each symbol:                                                    │
   │  ├── get_fundamentals(symbol)  ────→ NSE API ────→ TTL Cache (5min) │
   │  ├── get_volume_stats(symbol)  ────→ Yahoo Finance ─→ TTL Cache     │
   │  └── get_historical_comparison(symbol, days) ──→ Yahoo ─→ Cache     │
   └──────────────────────────────────────────────────────────────────────┘
              ↓
4. stocks_data list assembled (all stock dictionaries)
              ↓
5. calculate_aggregate_volume_indicator(stocks_data)
              ↓
6. create_weekly_turnover_chart(stocks_data)
              ↓
7. UI rendered with table, indicator card, and chart
```

### Batch Processing

```python
DEFAULT_BATCH_SIZE = 50    # Stocks per batch
DEFAULT_WORKERS = 5        # Concurrent threads
```

- Processes 50 stocks per batch
- 2-second delay between batches (rate limiting protection)
- 5 concurrent API calls per batch

---

## Individual Stock Metrics

### Price Metrics

#### Last Day Closing Price
```python
prev_close = priceInfo.get("previousClose")
```
**Source**: NSE API  
**Resolution**: Daily (previous trading day close)

#### Today's Open Price
```python
today_open = priceInfo.get("open")
```
**Source**: NSE API  
**Resolution**: Real-time (set at market open 9:15 AM)

#### Current Price
```python
last_price = priceInfo.get("lastPrice")
```
**Source**: NSE API  
**Resolution**: Real-time (updated every few seconds during market hours)

---

### Price Change Calculations

#### 1-Day Price Change (Absolute)
```python
price_change = last_price - prev_close
```
**Unit**: ₹ (Indian Rupees)

#### 1-Day Price Change (Percentage)
```python
price_change_pct = (price_change / prev_close) * 100
```
**Unit**: Percentage (%)  
**Display**: ▲ or ▼ with color coding (green/red)

---

### N-Day Historical Comparison

**User Input**: Days input field (default: 10, max: 365)

#### Historical Price
```python
hist = tk.history(period=f"{days + TRADING_DAYS_BUFFER}d", interval="1d")
# TRADING_DAYS_BUFFER = 10 extra days for weekends/holidays

# Correct offset: -(days+1) because -1 is today
target_index = -(days + 1)
old_price = float(hist['Close'].iloc[target_index])
```

**Source**: Yahoo Finance  
**Resolution**: Daily close prices

#### N-Day Change
```python
hist_change = current_price - old_price
hist_change_pct = (hist_change / old_price) * 100
```

---

### Volume Metrics

#### 30-Day Average Volume
```python
hist_30 = tk.history(period="30d", interval="1d")
avg_vol = float(hist_30["Volume"].mean())
```
**Source**: Yahoo Finance  
**Resolution**: 30 daily data points, simple arithmetic mean  
**Note**: Uses all trading days in the 30-day window

#### Today's Volume
```python
# Primary: Sum of 1-minute intraday volume
intraday = tk.history(period="1d", interval="1m")
todays_vol = float(intraday["Volume"].sum())

# Fallback: Daily volume if intraday fails
daily = tk.history(period="1d", interval="1d")
todays_vol = float(daily["Volume"].iloc[-1])
```
**Source**: Yahoo Finance  
**Resolution**: 1-minute bars aggregated (if available)

#### Volume Change Percentage
```python
vol_change_pct = ((todays_vol - avg_vol) / avg_vol) * 100
```
**Interpretation**:
- `+50%`: Today's volume is 50% higher than the 30-day average
- `-30%`: Today's volume is 30% lower than the 30-day average

---

### Fundamental Metrics

#### P/E Ratio (Price-to-Earnings)
```python
pe = metadata.get("pdSymbolPe") or metadata.get("pdSectorPe")
```
**Source**: NSE API  
**Calculation**: Done by NSE (Stock Price / Earnings Per Share)

#### EPS (Earnings Per Share)
```python
eps = last_price / pe   # Derived from P/E and price
```
**Source**: Calculated from NSE P/E  
**Note**: Trailing twelve months (TTM) EPS

#### Market Cap
```python
issued_shares = securityInfo.get("issuedSize")
mcap = issued_shares * last_price
```
**Source**: NSE (issued shares) × Current price  
**Unit**: Rupees (displayed in Crores: value / 10,000,000)

---

## Aggregate Volume Indicator

This is the main dashboard widget showing market-wide volume analysis.

### Core Calculation: Volume Ratio

```python
# Accumulate across all stocks
for stock in stocks_data:
    total_volume_today += today_volume    # Raw volume
    total_volume_avg += avg_volume        # Raw volume

# Pure volume ratio (no price mixing)
volume_ratio = total_volume_today / total_volume_avg
volume_pct_change = (volume_ratio - 1) * 100
```

**Why Pure Volume?**  
Using turnover (price × volume) for comparison introduces a timing issue: the average volume was recorded at different prices than today's price. Pure volume comparison is more accurate.

---

### Turnover Calculation (Consistent with Weekly Chart)

> **⚠️ DATA CONSISTENCY**: Both TURNOVER TODAY and the Weekly Chart use **Yahoo Finance data** (Close × Volume). This ensures exact match between them.

```python
# In get_volume_stats(), we calculate:
latest_row = hist_30.iloc[-1]
todays_turnover = float(latest_row["Close"]) * float(latest_row["Volume"])

turnovers = hist_30["Close"] * hist_30["Volume"]
avg_turnover = float(turnovers.mean())

# In calculate_aggregate_volume_indicator():
todays_turnover_yf = safe_float(stock.get("TODAYS_TURNOVER"))
avg_turnover_yf = safe_float(stock.get("AVG_TURNOVER"))

# Use Yahoo Finance turnover for consistency
if todays_turnover_yf is not None:
    total_turnover_today += todays_turnover_yf
else:
    # Fallback to calculated if Yahoo data missing
    total_turnover_today += current_price * today_volume
```

**Data Source**: Yahoo Finance (same as weekly chart)  
**Unit**: Rupees  
**Display Format**: `₹X,XXX Cr` or `₹X.XXK Cr`


---

### Market Cap Weighted Volume Change

```python
for stock in stocks_data:
    if market_cap is not None and vol_change_pct is not None:
        total_market_cap += market_cap
        weighted_vol_change += market_cap * vol_change_pct

mcap_weighted_vol_change = weighted_vol_change / total_market_cap
```

**Interpretation**:
- Large-cap stocks (Reliance, HDFC Bank) have more weight
- If MCAP weighted > regular: Large caps are more active
- If MCAP weighted < regular: Small/mid caps are more active

---

### Volume Breadth

```python
VOL_ELEVATED_THRESHOLD = 1.20   # 20% above average
VOL_REDUCED_THRESHOLD = 0.80    # 20% below average

volume_ratio_stock = today_volume / avg_volume

if volume_ratio_stock >= 1.20:
    stocks_elevated_vol += 1    # Significantly above average
elif volume_ratio_stock <= 0.80:
    stocks_reduced_vol += 1     # Significantly below average
else:
    stocks_normal_vol += 1      # Within normal range
```

**Display**: `X% (Y/Z)` where Y = stocks with elevated volume, Z = total stocks

---

### Flow Direction

```python
for stock in stocks_data:
    if price_change_pct > 0:
        up_turnover += turnover_today
        stocks_up += 1
    elif price_change_pct < 0:
        down_turnover += turnover_today
        stocks_down += 1

flow_ratio = (up_turnover / (up_turnover + down_turnover)) * 100
```

**Thresholds**:
| Flow Ratio | Status |
|------------|--------|
| ≥ 60% | 🟢 STRONG BUYING |
| 52-60% | 🟢 BUYING |
| 48-52% | ⚪ NEUTRAL |
| 40-48% | 🔴 SELLING |
| < 40% | 🔴 STRONG SELLING |

---

### Intraday Volume Projection

Uses **non-linear U-shaped distribution** based on actual Indian market patterns:

```python
INTRADAY_VOL_DISTRIBUTION = {
    (9, 15): 0.00,   # Market open - 0%
    (9, 30): 0.05,   # After 15 min - 5%
    (10, 0): 0.15,   # 45 min in - 15%
    (11, 0): 0.31,   # 1h 45m - 31%
    (12, 0): 0.44,   # Midday - 44%
    (13, 0): 0.56,   # 56%
    (14, 0): 0.69,   # 69%
    (15, 0): 0.91,   # Last 30 min - 91%
    (15, 30): 1.00,  # Close - 100%
}
```

**Projection Calculation**:
```python
expected_vol_factor = get_expected_volume_factor(current_time)
projected_volume = total_volume_today / expected_vol_factor
scaled_volume_ratio = projected_volume / total_volume_avg
```

**Example**: At 10:30 AM, expected = 23%. If actual volume is 20%, projection shows the market is running **below pace**.

---

### Alert Levels

| Volume Ratio | Alert | Color |
|--------------|-------|-------|
| ≥ 2.0× | 🔥 EXTREME VOLUME | Orange |
| 1.5× - 2.0× | 📈 VERY HIGH VOLUME | Green |
| 1.2× - 1.5× | ↗️ ELEVATED VOLUME | Light Green |
| 0.8× - 1.2× | ➡️ NORMAL VOLUME | Gray |
| 0.5× - 0.8× | ↘️ LOW VOLUME | Light Red |
| < 0.5× | 📉 VERY LOW VOLUME | Red |

---

## Weekly Turnover Chart

### Data Collection

```python
# During get_volume_stats(), collect last 7 days:
if len(hist_30) >= 7:
    last_7_days = hist_30.tail(7)
    for idx, row in last_7_days.iterrows():
        turnover = float(row["Close"]) * float(row["Volume"])
        weekly_turnover.append({"date": date_str, "turnover": turnover})
```

**Source**: Yahoo Finance 30-day historical data  
**Resolution**: Daily Close × Daily Volume

### Chart Creation

```python
def create_weekly_turnover_chart(stocks_data):
    # Aggregate turnover by date across ALL stocks
    daily_turnover = {}
    for stock in stocks_data:
        for day_data in stock["WEEKLY_TURNOVER"]:
            daily_turnover[date] += turnover
    
    # Create Plotly bar chart
    fig = go.Figure()
    fig.add_trace(go.Bar(
        x=display_dates,  # "Mon 09", "Tue 10", etc.
        y=turnovers,      # In Crores
        ...
    ))
```

**Display**: Bar chart showing 7 days of aggregate market turnover

---

## Caching Strategy

### TTL Cache Configuration

```python
CACHE_TTL_SECONDS = 300   # 5 minutes
CACHE_MAX_SIZE = 256      # Max items per cache

nse_data_cache = TTLCache(maxsize=256, ttl=300)
fundamentals_cache = TTLCache(maxsize=256, ttl=300)
historical_cache = TTLCache(maxsize=256, ttl=300)
volume_cache = TTLCache(maxsize=256, ttl=300)
```

### Cache Keys

| Cache | Key Format | Example |
|-------|------------|---------|
| NSE Data | `symbol` | `"RELIANCE"` |
| Fundamentals | `symbol` | `"TCS"` |
| Historical | `symbol_days` | `"INFY_10"` |
| Volume | `symbol` | `"HDFCBANK"` |

### Manual Cache Clear

Triggered on:
- Manual Refresh button click
- Auto-refresh interval (every 5 minutes)
- Days input change

```python
nse_data_cache.clear()
fundamentals_cache.clear()
volume_cache.clear()
historical_cache.clear()
```

---

## Market Hours & Timezone

### Configuration

```python
IST = pytz.timezone('Asia/Kolkata')

MARKET_OPEN_HOUR = 9
MARKET_OPEN_MINUTE = 15    # 9:15 AM IST
MARKET_CLOSE_HOUR = 15
MARKET_CLOSE_MINUTE = 30   # 3:30 PM IST
```

### Market Status Detection

```python
current_time = datetime.now(IST)
market_open = current_time.replace(hour=9, minute=15)
market_close = current_time.replace(hour=15, minute=30)

if market_open <= current_time <= market_close:
    # Market is open - show intraday projection
    ...
else:
    # Market is closed - show full-day data
    intraday_factor = 1.0
```

---

## Data Freshness Summary

| Metric | Update Frequency | API Source |
|--------|-----------------|------------|
| Current Price | Real-time (during market) | NSE |
| Today's Volume | Real-time (1-min aggregated) | Yahoo Finance |
| 30-Day Avg Volume | Daily | Yahoo Finance |
| P/E, EPS | Real-time | NSE |
| Weekly Turnover | Daily | Yahoo Finance |
| Industry Mapping | Static (from CSV) | Local file |

---

## Auto-Refresh

```python
dcc.Interval(
    id='auto-refresh-interval',
    interval=5*60*1000,  # 5 minutes = 300,000 ms
    n_intervals=0,
    disabled=True  # Enabled when industry selected
)
```

**Behavior**: Automatically refreshes all data every 5 minutes when an industry is selected.

---

## Error Handling

| Scenario | Handling |
|----------|----------|
| NSE API rate limit | Exponential backoff (0.5s, 1s, 2s, then skip) |
| Yahoo Finance failure | Fallback to daily data if intraday fails |
| Missing data fields | Return `None`, display as "-" |
| CSV not found | Log error, return empty mapping |
| Division by zero | Check conditions, return `None` |

---

## Performance Optimizations

1. **Parallel Fetching**: ThreadPoolExecutor with 5 workers
2. **Batch Processing**: 50 stocks per batch with 2s delay
3. **TTL Caching**: 5-minute cache reduces redundant API calls
4. **Static Data**: Industry mapping loaded from CSV (instant)
5. **Conditional Rendering**: Intraday section only shown during market hours

---

*Document generated: December 2025*
*Application version: 1.0 (with fixes applied)*
