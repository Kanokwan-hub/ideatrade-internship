# ideatrade-internship  
## Thai Stock Signal Prediction using Transformer & Technical Indicators

This project aims to develop a **Machine Learning model** using the **Transformer architecture** to predict `Buy / Hold / Sell` signals for Thai stocks. The model leverages a combination of **OHLCV data**, **tick-level trading data**, and **technical indicators** to identify precise stock entry and exit points for decision-making in trading.

---

## Workflow Overview

### Step 1: Load Stock Data from MySQL
- Retrieve daily OHLCV stock data from the `stock_hloc` table using `pymysql`
- Extract tick-level trade data from the `stocksm_tickmatchs` table using raw SQL
- Merge OHLCV and tick data into a unified DataFrame for feature engineering

### Step 2: Compute Technical Indicators
- Use the function `compute_indicators(df)` to calculate a wide range of commonly used technical indicators, including:
  - **RSI**, **MACD**, **PPO**, **ADX**, **ATR**, **StochRSI**, and more

### Step 3: Generate Labels using N-Period Min-Max (NPMM)
- Assign signal labels based on future price movement within a 15-day lookahead window:
  - **Buy** → Current price is the *lowest* within the next 15 days  
  - **Sell** → Current price is the *highest* within the next 15 days  
  - **Hold** → Otherwise

### Step 4: Create Sequential Data (X, y)
- Generate time-series sequences using a **15-day sliding window**
- Input features include over **35 variables** from OHLCV and Tick-based signals, such as:
  - `Buy/Sell Volume Ratio`  
  - `Bearish Engulfing`  
  - `Buy Dominance`  
  - `Long Upper Shadow`  
  - And others

### Step 5: Build & Train Transformer Model
- Construct a **Transformer model** using **PyTorch**
- Handle class imbalance using **SMOTE** or **RandomOversampler**
- Train using **CrossEntropy Loss** with optional label smoothing
- Evaluate model performance using:
  - **Accuracy**  
  - **F1-Score**  
  - **Confusion Matrix**  
  - **Feature Importance** (optional)

### Step 6: Train & Backtest
- Apply **Walk-Forward Expanding Window** strategy to train and validate the model sequentially over time
- Save the **best-performing Transformer model** and corresponding **label encoder**
- Conduct **backtesting** using real historical stock data:
  - Predict signals and overlay on price charts
  - Compute and visualize **cumulative returns**
  - Analyze **per-stock signal distribution** (Buy / Hold / Sell)
  - Display results using interactive or static visualizations
 
# Thai Stock Buy Signal Analysis and Exit Strategy Evaluation
This project analyzes **combined buy signals** from multiple analyst predictions on Thai stocks and evaluates their performance using several **exit strategies**, including trailing stops, sell signals, and fixed holding periods.
The workflow combines predictions from multiple CSV files, filters for `"BUY"` signals only, and applies various exit strategies to validate those signals. It also summarizes accuracy statistics per analyst and per stock.

---

## Data Input
- Load buy signal predictions from multiple CSV files (e.g. `pi_pred.csv`, `fns_pred.csv`, `king_pred.csv`, `aira_pred.csv`)
- Concatenate all files into a single DataFrame
- Filter to keep only rows where `action` contains `"BUY"`

---

## Signal Validation Logic

### Initial Prediction (`predict`)
- Check if `cs15` (closing price after 15 days) increases by at least **3%** from the entry price (`cs`)

### Trailing Stop Exit (`predict_trailing`)
- Apply a **5% trailing stop loss** after a cooldown of `sleep_days` (default: 5 days), check at day 5, 10, 15

### Sell Signal Exit (`predict_sell_signal`)
- Use actual **sell signals** after `sleep_days`, and check if gain is at least **5%**

### Fixed Horizon Gain (`predict_cs15`)
- Check if gain at day 15 is **≥ 7.5%**

### Combined Prediction (`predict_combined`)
- Logical **OR** combination of:
  - Trailing Stop
  - Sell Signal
  - Fixed Horizon Gain

---

## Functions and Methods

| Function | Description |
|---------|-------------|
| `trailing_stop(df, stop_pct=0.05, sleep_days=5)` | Compute exit days based on trailing stop logic |
| `sell_signal_exit(df, sleep_days=5)` | Identify exit days from actual sell signals |
| `combined_exit_strategy(df, stop_pct=0.05, sleep_days=5)` | Combine all logic into final prediction |
| `accuracy_per_stock_analyst(df, min_preds=3)` | Accuracy per `(stock, analyst)` pair |
| `compute_overall_analyst_accuracy()` | Accuracy summary per analyst |
| `calculate_holding_days(row)` | Estimate holding period until target gain |
| `get_avg_holding_days(stock_name=None, analyst=None)` | Query average holding duration |
| `get_analysts_for_stock(specific_stock, detailed_df)` | JSON summary of analyst performance for a stock |
