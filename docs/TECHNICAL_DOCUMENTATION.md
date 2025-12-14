# 📊 Turnexia Stock Screener - Technical Documentation

> **Complete guide to data sources, calculations, and architecture**

---

## Table of Contents

1. [Technology Stack](#technology-stack)
2. [Data Sources](#data-sources)
3. [Multi-Index Support](#multi-index-support)
4. [Performance Optimizations](#performance-optimizations)
5. [Aggregate Volume Indicator](#aggregate-volume-indicator)
6. [Index Candlestick Chart](#index-candlestick-chart)
7. [Weekly Turnover Chart](#weekly-turnover-chart)
8. [Caching Strategy](#caching-strategy)
9. [Market Hours & Timezone](#market-hours--timezone)

---

## Technology Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Frontend** | Dash (Plotly) | Interactive web UI |
| **UI Components** | dash-bootstrap-components | Bootstrap styling |
| **Charting** | Plotly Graph Objects | Candlesticks, bar charts |
| **Backend** | Python 3.8+ | Data processing |
| **NSE Data** | nsepython | Real-time NSE data |
| **Historical Data** | yfinance | Yahoo Finance OHLCV |
| **Caching** | cachetools (TTLCache) | 5-min / 1-hour TTL |
| **Concurrency** | threading | Lock for duplicate prevention |

### File Structure

```
stock_screener/
├── app_optimized.py              # Main Dash application
├── assets/
│   ├── logo.png                  # Turnexia brand logo
│   └── logo_symbol.png           # Logo symbol
├── docs/
│   ├── TRADERS_INDICATOR_GUIDE.md
│   ├── INDICATOR_GUIDE.md
│   └── TECHNICAL_DOCUMENTATION.md
├── requirements.txt
└── README.md
```

---

## Data Sources

### 1. NSE Python API (`nsepython`)

| Field | NSE JSON Path | Description |
|-------|--------------|-------------|
| Last Price | `priceInfo.lastPrice` | Current price |
| Previous Close | `priceInfo.previousClose` | Yesterday's close |
| Open | `priceInfo.open` | Today's open |
| P/E Ratio | `metadata.pdSymbolPe` | Price-to-Earnings |
| 52W High/Low | `priceInfo.weekHighLow` | Year range |
| Industry | `industryInfo.industry` | Sector classification |

**Rate Limiting**: Exponential backoff (0.5s, 1s, 2s)

### 2. Yahoo Finance API (`yfinance`)

**Ticker Format**: `{SYMBOL}.NS`

| Data Type | Period | Interval | Used For |
|-----------|--------|----------|----------|
| Historical | 30d | 1d | Volume averages, turnover |
| Index OHLC | 60d | 1d | Candlestick chart |
| Intraday | 1d | 1m | Today's volume (fallback) |

---

## Multi-Index Support

### Supported Indices

| Index | Symbol | Stocks |
|-------|--------|--------|
| NIFTY 50 | `NIFTY50` | 50 |
| NIFTY 100 | `NIFTY100` | 100 |

### Index Symbols for Charts

```python
INDEX_SYMBOLS = {
    "NIFTY50": "^NSEI",
    "NIFTY100": "^CNX100"
}
```

### Industry Loading Optimization

```python
# NIFTY50 symbols are a subset of NIFTY100
# Load NIFTY100 first, then reuse for NIFTY50

if index_name == "NIFTY50":
    # Check if NIFTY100 cache exists
    if "NIFTY100" in cache:
        # Reuse NIFTY100 industries (0 API calls)
        for symbol in nifty50_symbols:
            industry_map[symbol] = nifty100_cache[symbol]
    else:
        # Load NIFTY100 first, then reuse
        load_index_with_industries("NIFTY100")
```

**API Call Savings**: 50 fewer calls on startup

---

## Performance Optimizations

### 1. Threading Lock for Industry Loading

```python
_industry_load_lock = threading.Lock()

def load_index_with_industries(index_name):
    # Check cache before lock (fast path)
    if cache_key in cache:
        return cache[cache_key]
    
    # Acquire lock (prevents concurrent duplicates)
    with _industry_load_lock:
        # Double-check after lock
        if cache_key in cache:
            return cache[cache_key]
        
        # Fetch industries (only one thread does this)
        ...
```

### 2. Background Preloading

```python
def preload_all_indices():
    # Order: NIFTY100 first so NIFTY50 can reuse
    preload_order = ["NIFTY100", "NIFTY50"]
    
    for index in preload_order:
        load_index_with_industries(index)
    
    # Runs in background thread
    thread = threading.Thread(target=_preload, daemon=True)
    thread.start()
```

### 3. Parallel Data Fetching

```python
DEFAULT_WORKERS = 5    # Concurrent threads
DEFAULT_BATCH_SIZE = 50  # Stocks per batch
```

### 4. Cache Configuration

| Cache | TTL | Max Size |
|-------|-----|----------|
| Stock data | 5 min | 1000 |
| Index constituents | 1 hour | 10 |

---

## Aggregate Volume Indicator

### Core Metrics

| Metric | Calculation |
|--------|-------------|
| **Volume % Change** | `(total_today / total_avg - 1) × 100` |
| **MCAP Weighted** | `Σ(mcap × vol_change) / Σ(mcap)` |
| **Volume Breadth** | `stocks_elevated / total_stocks × 100` |
| **Flow Direction** | `up_turnover / total_turnover × 100` |
| **Turnover** | `Σ(close × volume)` (Yahoo Finance) |

### Alert Thresholds

| Volume Ratio | Alert Level |
|--------------|-------------|
| ≥ 2.0× | 🔥 EXTREME |
| 1.5× - 2.0× | 📈 VERY HIGH |
| 1.2× - 1.5× | ↗️ ELEVATED |
| 0.8× - 1.2× | ➡️ NORMAL |
| 0.5× - 0.8× | ↘️ LOW |
| < 0.5× | 📉 VERY LOW |

### Intraday Volume Distribution (U-Shape)

```python
INTRADAY_VOL_DISTRIBUTION = {
    (9, 15): 0.00,   # Market open
    (10, 0): 0.15,   # 15%
    (12, 0): 0.44,   # Midday lull
    (15, 0): 0.91,   # Closing rush
    (15, 30): 1.00   # Close
}
```

---

## Index Candlestick Chart

### Configuration

```python
INDEX_CANDLE_DAYS = 60    # Period for chart
EMA_PERIOD = 20           # EMA overlay
```

### Data Fetching

```python
def create_index_candlestick_chart(index_name):
    # Get Yahoo Finance symbol
    yf_symbol = INDEX_SYMBOLS.get(index_name)  # ^NSEI or ^CNX100
    
    # Fetch daily OHLC
    df = yf.download(yf_symbol, period="60d", interval="1d")
    
    # Calculate 20-day EMA
    df["EMA20"] = df["Close"].ewm(span=20).mean()
    
    # Create candlestick with EMA overlay
    fig = go.Figure()
    fig.add_trace(go.Candlestick(...))
    fig.add_trace(go.Scatter(y=df["EMA20"], mode="lines"))
```

### Chart Features

- Dark theme styling
- 60-day price change in header (%)
- Current price annotation
- Dotted yellow EMA line

---

## Weekly Turnover Chart

### Data Collection

```python
# During get_volume_stats()
for day in last_7_days:
    turnover = close_price × volume  # Yahoo Finance data
    weekly_turnover.append({"date": date, "turnover": turnover})
```

### Aggregation

```python
def create_weekly_turnover_chart(stocks_data):
    daily_turnover = {}
    for stock in stocks_data:
        for day in stock["WEEKLY_TURNOVER"]:
            daily_turnover[date] += day["turnover"]
    
    # Bar chart with gradient coloring
    fig.add_trace(go.Bar(...))
```

**Data Consistency**: Uses same Yahoo Finance source as TURNOVER TODAY metric.

---

## Caching Strategy

### Cache Types

```python
# Stock data caches (5-min TTL)
nse_data_cache = TTLCache(maxsize=1000, ttl=300)
fundamentals_cache = TTLCache(maxsize=1000, ttl=300)
historical_cache = TTLCache(maxsize=1000, ttl=300)
volume_cache = TTLCache(maxsize=1000, ttl=300)

# Index cache (1-hour TTL)
index_constituents_cache = TTLCache(maxsize=10, ttl=3600)
```

### Cache Keys

| Cache | Key Format |
|-------|------------|
| NSE Data | `symbol` |
| Historical | `symbol_days` |
| Index Industries | `index_industries_NIFTY100` |

### Auto-Clear Triggers

- Manual refresh button
- Auto-refresh interval (5 min)
- Days input change

---

## Market Hours & Timezone

```python
IST = pytz.timezone('Asia/Kolkata')

MARKET_OPEN = 9:15 AM IST
MARKET_CLOSE = 3:30 PM IST
```

### Market Status Detection

```python
if market_open <= current_time <= market_close:
    # Show intraday projection
else:
    # Show full-day data
```

---

## Error Handling

| Scenario | Handling |
|----------|----------|
| NSE rate limit | Exponential backoff |
| Yahoo failure | Fallback to daily data |
| Missing data | Return `None`, display "-" |
| Division by zero | Condition check |

---

## Data Freshness

| Metric | Frequency | Source |
|--------|-----------|--------|
| Current Price | Real-time | NSE |
| Today's Volume | Real-time | Yahoo |
| 10-Day Avg | Daily | Yahoo |
| Index Candlestick | Daily | Yahoo |
| Industry | Cached 1hr | NSE |

---

*Document version: 2.0 | December 2025*
