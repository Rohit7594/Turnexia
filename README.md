# 📊 Turnexia Stock Screener

A powerful, real-time stock screening and market analysis tool for **NIFTY 50** and **NIFTY 100** stocks built with Python, Dash, and Plotly. Get comprehensive market insights including volume trends, money flow analysis, and key technical indicators—all in a beautiful, modern interface.

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Dash](https://img.shields.io/badge/Dash-2.0+-green.svg)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)

---

## ✨ Features

### 🔄 Multi-Index Support
- **NIFTY 50** - Top 50 blue-chip stocks
- **NIFTY 100** - Extended coverage with 100 stocks
- Instant switching between indices with optimized caching

### 📈 Real-Time Stock Data
- **Live price updates** during market hours (9:15 AM - 3:30 PM IST)
- Current, Open, and Last Close prices with daily change tracking
- N-Day historical price comparison (configurable: 10, 30, 50, 200 days)
- 52-week high/low analysis

### 📊 Aggregate Volume Indicator
A market-wide dashboard providing institutional-grade volume analysis:

| Metric | Description |
|--------|-------------|
| **Volume % Change** | Total market volume vs 10-day average |
| **MCAP Weighted** | Volume weighted by market cap (institutional vs retail) |
| **Volume Breadth** | Percentage of stocks with above-average volume |
| **Flow Direction** | Money flowing into gainers vs losers |
| **Turnover** | Total traded value in ₹ Crores |
| **Intraday Projection** | Projected full-day volume (U-shape pattern) |

### 📈 Index Candlestick Chart
- **60-day daily candlestick** chart for selected index
- **20-day EMA** overlay for trend identification
- Professional dark theme styling

### 📉 Weekly Turnover Chart
- 7-day bar chart showing aggregate market turnover trends
- Uses Yahoo Finance data for consistency across all metrics

### 🏢 Fundamental Data
- Market Cap (in Crores), P/E Ratio, EPS
- Industry classification via NSE API

### 🔍 Filtering & Sorting
- **Filter by Industry**: Focus on specific sectors
- **"All Industries"**: View complete market aggregate
- **Sortable Columns**: Click any column header

---

## ⚡ Performance Optimizations

### Smart Caching Strategy
| Feature | Benefit |
|---------|---------|
| **NIFTY50 reuses NIFTY100 data** | 50 fewer API calls on startup |
| **Threading lock** | Prevents duplicate concurrent fetches |
| **TTL-based caching** | 5-min cache for stock data, 1-hour for indices |
| **Background preloading** | Non-blocking startup with instant index switching |

### API Call Reduction
- **Before**: 150+ industry API calls on startup
- **After**: Exactly 100 calls (NIFTY100 shared with NIFTY50)

---

## 🚀 Quick Start

### Prerequisites
- Python 3.8 or higher
- pip (Python package manager)

### Installation

```bash
# Clone the repository
git clone https://github.com/Rohit7594/stock_screener.git
cd stock_screener

# Create virtual environment (recommended)
python -m venv venv

# Windows
venv\Scripts\activate

# macOS/Linux
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Run the application
python app_optimized.py
```

Open `http://127.0.0.1:8051` in your browser.

---

## 📦 Dependencies

| Package | Purpose |
|---------|---------|
| `dash` | Web application framework |
| `dash-bootstrap-components` | Modern UI components |
| `pandas` | Data manipulation |
| `yfinance` | Yahoo Finance data API |
| `nsepython` | NSE India data API |
| `plotly` | Interactive charts |
| `cachetools` | TTL-based caching |
| `pytz` | Timezone handling |

---

## 📁 Project Structure

```
stock_screener/
├── app_optimized.py              # Main application (Dash server)
├── assets/
│   ├── logo.png                  # Turnexia brand logo
│   └── logo_symbol.png           # Logo symbol for UI elements
├── requirements.txt              # Python dependencies
├── docs/
│   ├── TRADERS_INDICATOR_GUIDE.md
│   ├── INDICATOR_GUIDE.md
│   └── TECHNICAL_DOCUMENTATION.md
└── README.md                     # This file
```

---

## 🎯 How to Use

### Dashboard Workflow

1. **Select Index** (NIFTY 50 or NIFTY 100) from the dropdown
2. **Select an Industry** or choose "All Industries" for market-wide view
3. **Monitor the Aggregate Volume Indicator** card for market pulse
4. **View Index Candlestick Chart** for price action trend
5. **Sort the table** by clicking column headers
6. **Auto-refresh** every 5 minutes or click ⟳ for manual refresh

### Trading Signal Reference

| Scenario | Volume | MCAP Weighted | Breadth | Flow | Signal |
|----------|--------|---------------|---------|------|--------|
| ✅ Breakout | +50% | +45% | 75% | 🟢 BUYING | High conviction |
| ⚠️ False Breakout | +40% | +5% | 35% | 🟢 BUYING | Wait |
| 🔴 Selloff | +25% | +30% | 60% | 🔴 SELLING | Defensive |
| 😐 Low Conviction | -30% | -25% | 25% | ⚪ NEUTRAL | Reduce size |

---

## ⏰ Data Refresh

| Data Type | Frequency |
|-----------|-----------|
| Stock prices | Real-time (market hours) |
| Volume metrics | Real-time |
| Index constituents | 1-hour cache |
| All indicators | Auto-refresh every 5 minutes |

---

## 🛠️ Configuration

### Key Constants (in `app_optimized.py`)
```python
CACHE_TTL_SECONDS = 300           # 5-minute cache for stock data
INDEX_CACHE_TTL = 3600            # 1-hour cache for index data
VOL_AVERAGE_DAYS = 10             # Days for volume average
VOL_ELEVATED_THRESHOLD = 1.20     # 20% above = elevated
INDEX_CANDLE_DAYS = 60            # Candlestick chart period
```

### Market Hours (IST)
- **Open**: 9:15 AM
- **Close**: 3:30 PM

---

## 🤝 Contributing

Contributions welcome! Fork → Create branch → Commit → Push → Pull Request

---

## 📄 License

MIT License - see [LICENSE](LICENSE) file.

---

## 🙏 Acknowledgments

- [Yahoo Finance](https://finance.yahoo.com/) for historical OHLC data
- [NSE India](https://www.nseindia.com/) for real-time market data
- [Plotly Dash](https://dash.plotly.com/) for the web framework

---

<p align="center">
  <b>Turnexia</b> - Made with ❤️ for Indian Stock Market Traders
</p>
