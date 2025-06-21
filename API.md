# AI Traders - API Documentation

## Overview

The AI Traders provides a comprehensive API for managing AI-powered trading agents, monitoring portfolios, and executing trades. The system consists of multiple components that work together to create a realistic trading environment.

## Table of Contents

1. [Core Classes](#core-classes)
2. [Trader Class](#trader-class)
3. [Account Class](#account-class)
4. [Transaction Model](#transaction-model)
5. [Database Operations](#database-operations)
6. [Market Data](#market-data)
7. [Web Dashboard API](#web-dashboard-api)
8. [Configuration](#configuration)

---

## Core Classes

### Trader Class

The main class representing an AI trading agent with portfolio management capabilities.

#### Constructor
```python
Trader(name: str, lastname: str, model_name: str)
```

**Parameters:**
- `name` (str): Trader's first name (e.g., "Warren", "George")
- `lastname` (str): Trader's last name/strategy type (e.g., "Patience", "Bold")
- `model_name` (str): AI model identifier (e.g., "gpt-4o-mini")

**Example:**
```python
trader = Trader("Warren", "Patience", "gpt-4o-mini")
```

#### Methods

##### `reload()`
Reloads the trader's account data from the database.

**Returns:** None

##### `get_title() -> str`
Generates HTML title for the trader's dashboard panel.

**Returns:** HTML string with trader name, model, and strategy

**Example Output:**
```html
<div style='text-align: center;font-size:34px;'>Warren<span style='color:#ccc;font-size:24px;'> (gpt-4o-mini) - Patience</span></div>
```

##### `get_strategy() -> str`
Retrieves the trader's current investment strategy.

**Returns:** Strategy description string

##### `get_portfolio_value_df() -> pd.DataFrame`
Converts portfolio value time series to a pandas DataFrame.

**Returns:** DataFrame with columns ["datetime", "value"]

##### `get_portfolio_value_chart()`
Creates a Plotly line chart of portfolio value over time.

**Returns:** Plotly figure object

**Chart Features:**
- Height: 300px
- Background colors: #bbb (paper), #dde (plot)
- X-axis: Date format (MM/DD), 45° angle
- Y-axis: Comma-separated numbers

##### `get_holdings_df() -> pd.DataFrame`
Converts current holdings to a display DataFrame.

**Returns:** DataFrame with columns ["Symbol", "Quantity"]

**Empty State:** Returns empty DataFrame with specified columns

##### `get_transactions_df() -> pd.DataFrame`
Converts transaction history to a display DataFrame.

**Returns:** DataFrame with columns ["Timestamp", "Symbol", "Quantity", "Price", "Rationale"]

**Empty State:** Returns empty DataFrame with specified columns

##### `get_portfolio_value() -> str`
Calculates and formats current portfolio value with profit/loss.

**Returns:** HTML string with portfolio value and P&L

**Format:**
- Green background for positive P&L, red for negative
- Portfolio value in large font (32px)
- P&L with arrow emoji (⬆/⬇) in smaller font (24px)

**Example Output:**
```html
<div style='text-align: center;background-color:green;'>
  <span style='font-size:32px'>$15,250</span>
  <span style='font-size:24px'>&nbsp;&nbsp;&nbsp;⬆&nbsp;$5,250</span>
</div>
```

##### `get_logs(previous=None) -> str`
Retrieves and formats recent activity logs.

**Parameters:**
- `previous` (str, optional): Previous log content for change detection

**Returns:** HTML string with color-coded logs or `gr.update()` if unchanged

**Log Types and Colors:**
- `trace`: White (#87CEEB)
- `agent`: Cyan (#00dddd)
- `function`: Green (#00dd00)
- `generation`: Yellow (#dddd00)
- `response`: Magenta (#aa00dd)
- `account`: Red (#dd0000)

---

### Account Class

Pydantic model representing a trading account with portfolio management.

#### Constructor
```python
Account.get(name: str) -> Account
```

**Parameters:**
- `name` (str): Account name (converted to lowercase)

**Returns:** Account instance with default values if new

**Default Values:**
- Balance: $10,000
- Strategy: Empty string
- Holdings: Empty dict
- Transactions: Empty list
- Portfolio time series: Empty list

#### Properties

| Property | Type | Description |
|----------|------|-------------|
| `name` | str | Account name |
| `balance` | float | Current cash balance |
| `strategy` | str | Investment strategy description |
| `holdings` | dict[str, int] | Stock symbols and quantities |
| `transactions` | list[Transaction] | Transaction history |
| `portfolio_value_time_series` | list[tuple[str, float]] | Portfolio value over time |

#### Methods

##### `save()`
Saves account data to the database.

**Returns:** None

##### `reset(strategy: str)`
Resets account to initial state with new strategy.

**Parameters:**
- `strategy` (str): New investment strategy

**Returns:** None

##### `deposit(amount: float)`
Adds funds to the account.

**Parameters:**
- `amount` (float): Deposit amount (must be positive)

**Raises:** ValueError if amount ≤ 0

**Returns:** None

##### `withdraw(amount: float)`
Removes funds from the account.

**Parameters:**
- `amount` (float): Withdrawal amount

**Raises:** ValueError if insufficient funds

**Returns:** None

##### `buy_shares(symbol: str, quantity: int, rationale: str) -> str`
Purchases shares of a stock.

**Parameters:**
- `symbol` (str): Stock symbol (e.g., "AAPL")
- `quantity` (int): Number of shares to buy
- `rationale` (str): Reason for purchase

**Returns:** Success message with account report

**Trading Logic:**
- Buy price = Market price × (1 + 0.002 spread)
- Validates sufficient funds
- Updates holdings and balance
- Records transaction with timestamp

**Raises:** ValueError for insufficient funds or invalid symbol

##### `sell_shares(symbol: str, quantity: int, rationale: str) -> str`
Sells shares of a stock.

**Parameters:**
- `symbol` (str): Stock symbol
- `quantity` (int): Number of shares to sell
- `rationale` (str): Reason for sale

**Returns:** Success message with account report

**Trading Logic:**
- Sell price = Market price × (1 - 0.002 spread)
- Validates sufficient shares
- Updates holdings and balance
- Records transaction with negative quantity

**Raises:** ValueError for insufficient shares

##### `calculate_portfolio_value() -> float`
Calculates total portfolio value including cash and holdings.

**Returns:** Total portfolio value in USD

##### `calculate_profit_loss(portfolio_value: float) -> float`
Calculates profit/loss from initial investment.

**Parameters:**
- `portfolio_value` (float): Current portfolio value

**Returns:** Profit/loss amount (positive = profit, negative = loss)

##### `get_holdings() -> dict[str, int]`
Returns current stock holdings.

**Returns:** Dictionary mapping symbols to quantities

##### `list_transactions() -> list[dict]`
Returns transaction history as dictionaries.

**Returns:** List of transaction dictionaries

##### `report() -> str`
Generates comprehensive account report as JSON.

**Returns:** JSON string with account details, portfolio value, and P&L

##### `get_strategy() -> str`
Returns current investment strategy.

**Returns:** Strategy description string

##### `change_strategy(strategy: str) -> str`
Updates the investment strategy.

**Parameters:**
- `strategy` (str): New strategy description

**Returns:** Confirmation message

---

### Transaction Model

Pydantic model representing a single trading transaction.

#### Properties

| Property | Type | Description |
|----------|------|-------------|
| `symbol` | str | Stock symbol |
| `quantity` | int | Number of shares (positive for buy, negative for sell) |
| `price` | float | Price per share |
| `timestamp` | str | Transaction timestamp (YYYY-MM-DD HH:MM:SS) |
| `rationale` | str | Reason for transaction |

#### Methods

##### `total() -> float`
Calculates total transaction value.

**Returns:** Quantity × Price

##### `__repr__() -> str`
String representation of the transaction.

**Returns:** Human-readable transaction description

---

## Database Operations

### Log Management

#### `read_log(name: str, last_n: int = 10) -> list[tuple]`
Retrieves recent log entries for a trader.

**Parameters:**
- `name` (str): Trader name
- `last_n` (int): Number of recent entries to retrieve

**Returns:** List of tuples (datetime, type, message)

#### `write_log(name: str, type: str, message: str)`
Writes a log entry to the database.

**Parameters:**
- `name` (str): Trader name
- `type` (str): Log type (trace, agent, function, etc.)
- `message` (str): Log message

**Returns:** None

### Account Management

#### `read_account(name: str) -> dict`
Retrieves account data from database.

**Parameters:**
- `name` (str): Account name

**Returns:** Account dictionary or None if not found

#### `write_account(name: str, account_dict: dict)`
Saves account data to database.

**Parameters:**
- `name` (str): Account name
- `account_dict` (dict): Account data dictionary

**Returns:** None

---

## Market Data

### `get_share_price(symbol: str) -> float`
Retrieves current share price for a stock symbol.

**Parameters:**
- `symbol` (str): Stock symbol (e.g., "AAPL", "TSLA")

**Returns:** Current share price in USD

**Fallback:** Returns random price (1-100) if API unavailable

### `is_market_open() -> bool`
Checks if the stock market is currently open.

**Returns:** True if market is open, False otherwise

---

## Web Dashboard API

### TraderView Class

Manages the UI components for a single trader's dashboard.

#### Constructor
```python
TraderView(trader: Trader)
```

**Parameters:**
- `trader` (Trader): Trader instance to display

#### Methods

##### `make_ui()`
Creates the Gradio UI components for the trader dashboard.

**Components:**
- Title (HTML)
- Portfolio value display (HTML)
- Portfolio chart (Plotly)
- Activity logs (HTML)
- Holdings table (DataFrame)
- Transactions table (DataFrame)

**Auto-refresh:**
- Data refresh: Every 120 seconds
- Log refresh: Every 0.5 seconds

##### `refresh() -> tuple`
Refreshes all dashboard data.

**Returns:** Tuple of updated components (portfolio_value, chart, holdings, transactions)

### Main UI

#### `create_ui() -> gr.Blocks`
Creates the main Gradio interface with all trader dashboards.

**Features:**
- Title: "Traders"
- Theme: Sky blue primary color
- Full width layout
- Responsive design

**Returns:** Gradio Blocks interface

---

## Configuration

### Environment Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `POLYGON_API_KEY` | None | Polygon.io API key for market data |
| `POLYGON_PLAN` | "basic" | Polygon plan type (basic/paid/realtime) |
| `RUN_EVERY_N_MINUTES` | 60 | Trading decision frequency |
| `RUN_EVEN_WHEN_MARKET_IS_CLOSED` | false | Trade outside market hours |
| `USE_MANY_MODELS` | false | Use different AI models per trader |

### Trading Parameters

| Parameter | Value | Description |
|-----------|-------|-------------|
| `INITIAL_BALANCE` | $10,000 | Starting balance per trader |
| `SPREAD` | 0.002 | Trading spread (0.2%) |
| `MAX_TURNS` | 30 | Maximum AI conversation turns |

### Default Traders

| Name | Lastname | Strategy | Model |
|------|----------|----------|-------|
| Warren | Patience | Value investing | gpt-4o-mini |
| George | Bold | Aggressive trading | gpt-4o-mini |
| Ray | Systematic | Algorithmic | gpt-4o-mini |
| Cathie | Crypto | Tech/crypto focus | gpt-4o-mini |

---

## Usage Examples

### Basic Setup
```python
from app import create_ui

# Create and launch the dashboard
ui = create_ui()
ui.launch(inbrowser=True)
```

### Create a Trader
```python
from app import Trader

# Create a new trader
trader = Trader("John", "Conservative", "gpt-4o-mini")

# Get portfolio information
portfolio_value = trader.get_portfolio_value()
holdings = trader.get_holdings_df()
transactions = trader.get_transactions_df()
```

### Account Operations
```python
from accounts import Account

# Get or create account
account = Account.get("john")

# Make trades
account.buy_shares("AAPL", 10, "Strong earnings report")
account.sell_shares("TSLA", 5, "Taking profits")

# Check portfolio
value = account.calculate_portfolio_value()
pnl = account.calculate_profit_loss(value)
```

### Market Data
```python
from market import get_share_price, is_market_open

# Check market status
if is_market_open():
    price = get_share_price("AAPL")
    print(f"AAPL price: ${price}")
```

---

## Error Handling

### Common Exceptions

| Exception | Cause | Solution |
|-----------|-------|----------|
| `ValueError` | Insufficient funds | Check account balance |
| `ValueError` | Invalid stock symbol | Verify symbol exists |
| `ValueError` | Insufficient shares | Check current holdings |
| `ConnectionError` | API unavailable | Check internet connection |
| `KeyError` | Missing environment variable | Set required API keys |

### Logging

All operations are logged with:
- Timestamp
- Operation type
- Trader name
- Detailed message

Log types are color-coded in the dashboard for easy identification.

---

## Performance Considerations

- **Database Operations**: SQLite with connection pooling
- **Market Data**: Cached with LRU cache (max 2 entries)
- **UI Updates**: Asynchronous with configurable intervals
- **Memory Usage**: Efficient DataFrame operations
- **API Rate Limits**: Built-in error handling and fallbacks

---

## Security Notes

- API keys should be stored in environment variables
- Database file should be protected from unauthorized access
- All user inputs are validated before processing
- No sensitive data is logged or displayed
- Trading operations are simulated only

---

*This documentation covers the core API functionality. For advanced features and customization, refer to the source code and configuration files.* 
