# Crypto Analytics Dashboard 📈

A robust, automated cryptocurrency data pipeline that collects, analyzes, and visualizes real-time market data from CoinMarketCap API. Built with Python, automated via GitHub Actions, and deployed on Streamlit Cloud.

### **[Streamlit Dashboard 🌐](https://coinmarketcapanalyticsdashboard.streamlit.app/)**

---

## 🎯 What This Does

This system automatically fetches cryptocurrency market data every 6 minutes, processes it, and presents it through an interactive web dashboard - all without manual intervention. Think of it as your personal crypto market analyst that never sleeps.

---

## 🚀 Key Features

- **Automated Data Collection**: Fetches top 100 cryptocurrencies data every 6 minutes
- **Real-time Dashboard**: Live web dashboard with automatic refresh
- **Zero Maintenance**: Fully automated workflow using GitHub Actions
- **Professional Analytics**: Market metrics, top gainers/losers, volume leaders
- **Historical Analysis**: Integrated TradingView charts for technical analysis

---

## 🛠️ Tech Stack

| Component | Technology | Purpose |
|-----------|------------|---------|
| **Data Source** | CoinMarketCap API | Real-time crypto market data |
| **Automation** | GitHub Actions | Cloud-based scheduled execution |
| **Processing** | Python (pandas, requests) | Data transformation & cleaning |
| **Dashboard** | Streamlit | Interactive web interface |
| **Deployment** | Streamlit Cloud | Free hosting with auto-sync |
| **Storage** | GitHub Repository | Version-controlled data storage |

---

## 📁 Project Structure

```
automated-crypto-market/
├── .github/
│   └── workflows/
│       └── scheduled-data-refresh.yml    # GitHub Actions automation
├── .gitignore
├── analysis/
│   ├── cleaned-data/
│   │   └── cleaned_data.csv             # Processed data for analysis
│   ├── database/
│   │   └── crypto_data.db               # Local SQLite database
│   └── local-automation/
│       └── csv_collector.py              # Local data collector script
├── dashboard/
│   └── dashboard.py                      # Streamlit dashboard application
├── img-resources/
│   └── icon-website.png                  # favicon assets
├── latest-data/
│   ├── cmc_latest_data_puller.py        # API data fetcher
│   └── latest_data.csv                   # Current market snapshot
├── scripts/
│   └── create_data.ipynb                # Jupyter notebook for analysis
├── requirements.txt                       # Python dependencies
└── README.md
```

---

## 🔄 How It Works

### The Data Flow

1. **Scheduled Trigger**: GitHub Actions runs every 6 minutes (`cron: '*/6 * * * *'`)
2. **API Call**: Python script fetches latest data from CoinMarketCap
3. **Data Processing**: Raw JSON is cleaned and structured into CSV format
4. **Auto-commit**: GitHub Actions commits updated data back to repository
5. **Dashboard Refresh**: Streamlit Cloud detects changes and updates the live dashboard

### Core Components Explained

#### Data Fetcher (`cmc_latest_data_puller.py`)
```python
def fetch_data(limit=100):
    """Simple API call with error handling"""
    url = 'https://pro-api.coinmarketcap.com/v1/cryptocurrency/listings/latest'
    headers = {'X-CMC_PRO_API_KEY': os.getenv('CMC_PRO_API_KEY')}
    response = requests.get(url, headers=headers, params={'limit': limit})
    return response.json()
```
This function handles the API communication. The API key is stored securely as a GitHub secret, never exposed in code.

#### GitHub Actions Workflow
```yaml
on:
  schedule:
    - cron: '*/6 * * * *'  # Runs every 6 minutes
permissions:
  contents: write          # Required for auto-commit
```
The workflow needs write permissions to update the CSV file automatically. The `[skip ci]` tag in commit messages prevents infinite loops.

#### Dashboard (`dashboard.py`)
The dashboard uses `@st.cache_data(ttl=360)` for performance optimization, caching data for 6 minutes (matching our update frequency). It displays:
- Global market metrics (total volume, market cap)
- Top 5 gainers and losers
- Interactive comparison charts
- Real-time TradingView integration

---

## 🚦 Quick Start

### Prerequisites
- GitHub account
- CoinMarketCap API key (free tier works)
- Streamlit Cloud account (free)

### Setup Steps

1. **Fork this repository**
   ```bash
   git clone https://github.com/yourusername/automated-crypto-market.git
   cd automated-crypto-market
   ```

2. **Add API Key to GitHub Secrets**
   - Go to Settings → Secrets → Actions
   - Add new secret: `CMC_PRO_API_KEY` = `your-api-key-here`

3. **Deploy to Streamlit Cloud**
   - Connect your GitHub repo to Streamlit Cloud
   - Deploy from `dashboard/dashboard.py`
   - Dashboard auto-updates when data changes

4. **Enable GitHub Actions**
   - Go to Actions tab in your repo
   - Enable workflows if prompted

That's it! Your dashboard will start updating automatically.

---

## 📊 Dashboard Features

### Market Overview
- **Global Metrics**: Total trading volume and market capitalization
- **Price Movements**: Real-time percentage changes (1h, 24h, 7d)
- **Volume Leaders**: Top cryptocurrencies by trading activity

### Interactive Analysis
- **Custom Comparisons**: Select multiple coins to compare metrics
- **Historical Charts**: Embedded TradingView widgets for technical analysis
- **Data Table**: Sortable, searchable complete dataset

### Visual Design
- **Responsive Layout**: Works on desktop, tablet, and mobile
- **Dark Theme**: Easy on the eyes for extended viewing
- **Smooth Animations**: Professional UI transitions

---

## 💻 Local Development

### Running Dashboard Locally
```bash
# Install dependencies
pip install -r requirements.txt

# Run Streamlit app
streamlit run dashboard/dashboard.py
```

### Local Data Collection Setup
For continuous data collection on your local machine:

```bash
# Navigate to analysis folder
cd analysis/local-automation

# Run collector (or set up with Task Scheduler)
python csv_collector.py
```

---

## ⚙️ Configuration

### Environment Variables
Create `.env` file for local development:
```env
CMC_PRO_API_KEY=your_api_key_here
```

### Custom Update Intervals
Modify the cron schedule in `.github/workflows/scheduled-data-refresh.yml`:
```yaml
- cron: '*/15 * * * *'  # Every 15 minutes
- cron: '0 * * * *'     # Every hour
```

### Adding More Coins
Change the limit in `cmc_latest_data_puller.py`:
```python
raw_data = fetch_data(limit=200)  # Fetch top 200 coins
```

---

## 📈 Data Analysis Workflow

### Local Analysis Pipeline
1. **Data Collection**: `csv_collector.py` → SQLite database
2. **Data Extraction**: Database → `cleaned_data.csv`
3. **Analysis**: Jupyter notebooks in `scripts/`
4. **Visualization**: Generate charts with matplotlib/seaborn

### Sample Analysis Code
```python
import pandas as pd
import matplotlib.pyplot as plt

# Load cleaned data
df = pd.read_csv('analysis/cleaned-data/cleaned_data.csv')

# Calculate volatility
df['volatility'] = df['percent_change_24h'].abs()

# Top 10 most volatile coins
top_volatile = df.nlargest(10, 'volatility')
```

---

## ⚠️ Current Limitations

1. **Update Frequency**: Limited to 6-minute intervals (GitHub Actions constraint)
2. **Historical Data**: Dashboard shows only current snapshot (use local workflow for historical analysis)
3. **API Rate Limits**: Free tier limits may apply for heavy usage
4. **Data Gaps**: Local collection depends on machine uptime

---

## 🚀 Future Enhancements

- **24/7 Data Collection**: Implement AWS Lambda for gap-free historical data
- **Technical Indicators**: Add RSI, MACD, Bollinger Bands calculations
- **Alert System**: Telegram/Slack notifications for price movements
- **Machine Learning**: Price prediction models using historical data
- **Portfolio Tracking**: Personal portfolio management features
- **Multi-exchange Support**: Aggregate data from multiple exchanges

---

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

### Development Guidelines
- Follow PEP 8 for Python code
- Add tests for new features
- Update documentation for API changes
- Create feature branches from `main`

---

## 📝 License

MIT License - feel free to use this project for personal or commercial purposes.

---

## 🙏 Acknowledgments

- CoinMarketCap for providing comprehensive crypto data
- Streamlit team for the amazing framework
- GitHub Actions for free CI/CD
- TradingView for embeddable charts

---

## 📧 Contact

For questions or suggestions, please open an issue or contact via [LinkedIn](https://www.linkedin.com/in/michaelvincentsebastian/) 😄.

---

**Note**: This project is for educational purposes. Always do your own research before making investment decisions.

Built with ❤️ by developers who believe in open-source and transparent crypto analytics.
