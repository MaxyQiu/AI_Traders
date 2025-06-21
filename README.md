# AI Trading Simulation

A sophisticated AI-powered stock trading simulation where multiple AI agents compete in the stock market using different strategies and AI models.

## 🚀 Features

- **4 AI Traders** with unique personalities and strategies
- **Real Market Data** via Polygon API
- **Web Dashboard** for real-time monitoring
- **AI Research Capabilities** with web search and knowledge graphs
- **Competitive Environment** with live portfolio tracking
- **Multiple AI Models** support (OpenAI, DeepSeek, Google, Grok)

## 🏗️ Architecture

### AI Traders
- **Warren (Patience)** - Value investing, long-term approach
- **George (Bold)** - Aggressive trading, high-risk strategies  
- **Ray (Systematic)** - Algorithmic, data-driven approach
- **Cathie (Crypto)** - Focus on tech and cryptocurrency-related stocks

### Components
- **Web Dashboard** (`app.py`) - Real-time monitoring interface
- **Trading Engine** (`trading_floor.py`) - AI traders making decisions
- **Account Management** (`accounts.py`) - Portfolio and transaction tracking
- **Market Data** (`market.py`) - Real-time stock price feeds
- **Database** (`database.py`) - SQLite storage for accounts and logs

## 📋 Prerequisites

- Python 3.8+
- API keys for:
  - Polygon (market data)
  - OpenAI (or other AI models)
  - DeepSeek, Google, Grok (optional for multi-model mode)

## 🛠️ Installation

1. **Clone the repository**
   ```bash
   git clone <your-repo-url>
   cd ai-trading-simulation
   ```

2. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

3. **Set up environment variables**
   ```bash
   cp env.example .env
   # Edit .env with your API keys
   ```

4. **Required API Keys**
   - `POLYGON_API_KEY` - Get from [Polygon.io](https://polygon.io/)
   - `OPENAI_API_KEY` - Get from [OpenAI](https://openai.com/)
   - Optional: `DEEPSEEK_API_KEY`, `GOOGLE_API_KEY`, `GROK_API_KEY`

## 🚀 Usage

### Start the Trading Engine
```bash
python trading_floor.py
```
This starts the AI traders who will make trading decisions every 60 minutes.

### Launch the Web Dashboard
```bash
python app.py
```
This opens a web interface at `http://localhost:7860` to monitor the traders.

## 📊 Dashboard Features

- **Real-time Portfolio Values** with profit/loss indicators
- **Performance Charts** showing portfolio value over time
- **Current Holdings** table with stock positions
- **Transaction History** with buy/sell activities
- **Live Activity Logs** with color-coded events

## ⚙️ Configuration

### Environment Variables
- `RUN_EVERY_N_MINUTES` - How often traders make decisions (default: 60)
- `RUN_EVEN_WHEN_MARKET_IS_CLOSED` - Trade outside market hours (default: false)
- `USE_MANY_MODELS` - Use different AI models for each trader (default: false)
- `POLYGON_PLAN` - Market data plan (basic/paid/realtime)

### Trading Parameters
- **Initial Balance**: $10,000 per trader
- **Trading Spread**: 0.2% (simulates real trading costs)
- **Market Hours**: Respects actual market open/close times

## 🔧 Technical Details

### AI Capabilities
Each trader has access to:
- Web research tools for financial news
- Real-time market data
- Trading execution tools
- Knowledge graph for persistent memory
- Push notifications for activity summaries

### Data Sources
- **Polygon API** - Real stock market data
- **Web Search** - Financial news and research
- **SQLite Database** - Account and transaction storage

## 📁 Project Structure

```
├── app.py                 # Main web dashboard
├── trading_floor.py       # AI trading engine
├── accounts.py           # Account management
├── market.py             # Market data handling
├── database.py           # Database operations
├── traders.py            # AI trader logic
├── templates.py          # AI instructions
├── util.py              # UI utilities
├── requirements.txt      # Dependencies
├── env.example          # Environment template
└── README.md            # This file
```

## 🤝 Contributing 

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

This project is licensed under the MIT License.

## ⚠️ Disclaimer

This is a simulation for educational and research purposes. It does not involve real money trading. Always do your own research before making actual investment decisions.
