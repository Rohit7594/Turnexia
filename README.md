# 📊 AKS Market Screener

A powerful, real-time stock screening and market analysis tool for **NIFTY 100** stocks built with Python, Dash, and Plotly. Get comprehensive market insights including volume trends, money flow analysis, and key technical indicators—all in a beautiful, modern interface.

![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)
![Dash](https://img.shields.io/badge/Dash-2.0+-green.svg)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)

---

## ✨ Features

### 📈 Real-Time Stock Data
- **Live price updates** during market hours (9:15 AM - 3:30 PM IST)
- Current, Open, and Last Close prices with daily change tracking
- N-Day historical price comparison (configurable: 10, 30, 50, 200 days)
- 52-week high/low analysis

### 📊 Aggregate Volume Indicator
A market-wide dashboard providing institutional-grade volume analysis:

| Metric | Description |
|--------|-------------|
| **Volume % Change** | Total market volume vs 30-day average |
| **MCAP Weighted** | Volume weighted by market cap (institutional vs retail activity) |
| **Volume Breadth** | Percentage of stocks with above-average volume |
| **Flow Direction** | Money flowing into gainers vs losers |
| **Turnover** | Total traded value in ₹ Crores |
| **Intraday Projection** | Projected full-day volume (accounts for U-shape pattern) |

### 📉 Weekly Turnover Chart
- 7-day bar chart showing aggregate market turnover trends
- Helps identify high/low activity days and patterns

### 🏢 Fundamental Data
- Market Cap (in Crores)
- P/E Ratio
- Earnings Per Share (EPS)
- Industry classification

### 🔍 Filtering & Sorting
- **Filter by Industry**: Focus on specific sectors
- **Custom Days Comparison**: Analyze price movement over your preferred timeframe
- **Sortable Columns**: Click any column header to sort

---

## 🚀 Quick Start

### Prerequisites
- Python 3.8 or higher
- pip (Python package manager)

### Installation

1. **Clone the repository**
   ```bash
   git clone https://github.com/Rohit7594/stock_screener.git
   cd stock_screener
   ```

2. **Create a virtual environment (recommended)**
   ```bash
   python -m venv venv
   
   # Windows
   venv\Scripts\activate
   
   # macOS/Linux
   source venv/bin/activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **Run the application**
   ```bash
   python app_optimized.py
   ```

5. **Open in browser**
   Navigate to `http://127.0.0.1:8050` in your web browser.

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
├── requirements.txt              # Python dependencies
├── nifty100.csv                  # NIFTY 100 stock symbols
├── nifty100_with_industries.csv  # Symbols with industry mapping
├── TRADERS_INDICATOR_GUIDE.md    # Detailed trading guide
├── INDICATOR_GUIDE.md            # Indicator explanations
├── TECHNICAL_DOCUMENTATION.md    # Technical implementation docs
└── README.md                     # This file
```

---

## 📖 Documentation

| Document | Description |
|----------|-------------|
| [Trader's Indicator Guide](TRADERS_INDICATOR_GUIDE.md) | Complete guide to understanding every metric |
| [Technical Documentation](TECHNICAL_DOCUMENTATION.md) | Implementation details and data sources |
| [Indicator Guide](INDICATOR_GUIDE.md) | Quick reference for all indicators |

---

## 🎯 How to Use

### Understanding the Dashboard

1. **Select an Industry** from the dropdown to filter stocks
2. **Adjust Days for Comparison** (default: 10 days) for historical analysis
3. **Monitor the Aggregate Volume Indicator** for market-wide insights
4. **Sort the table** by clicking column headers
5. **Refresh data** using the ⟳ button for latest prices

### Trading Scenarios

| Scenario | Volume | MCAP Weighted | Breadth | Flow | Action |
|----------|--------|---------------|---------|------|--------|
| ✅ Breakout | +50% | +45% | 75% | 🟢 BUYING | High conviction entry |
| ⚠️ False Breakout | +40% | +5% | 35% | 🟢 BUYING | Wait for confirmation |
| 🔴 Selloff | +25% | +30% | 60% | 🔴 SELLING | Defensive mode |
| 😐 Low Conviction | -30% | -25% | 25% | ⚪ NEUTRAL | Reduce position sizes |

---

## ⏰ Data Refresh

| Data Type | Update Frequency |
|-----------|-----------------|
| Stock prices | Real-time (during market hours) |
| Volume | Real-time |
| All indicators | Auto-refresh every 5 minutes |
| Manual refresh | Click ⟳ button |

---

## 🛠️ Configuration

### Cache Settings
The app uses TTL (Time-To-Live) caching to optimize API calls:
- **Cache TTL**: 2 minutes (configurable in `app_optimized.py`)
- **Max Cache Size**: 1000 entries

### Market Hours
Configured for NSE India:
- **Market Open**: 9:15 AM IST
- **Market Close**: 3:30 PM IST

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## 🙏 Acknowledgments

- [Yahoo Finance](https://finance.yahoo.com/) for historical data
- [NSE India](https://www.nseindia.com/) for real-time market data
- [Plotly Dash](https://dash.plotly.com/) for the excellent framework

---

## 📧 Contact

**Author**: Rohit Kumar Singh  
**GitHub**: [@Rohit7594](https://github.com/Rohit7594)

---

<p align="center">
  Made with ❤️ for Indian Stock Market Traders
</p>
