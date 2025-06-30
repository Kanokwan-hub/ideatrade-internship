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
