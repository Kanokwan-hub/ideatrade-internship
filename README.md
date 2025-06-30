# ideatrade-internship
# Thai Stock Signal Prediction using Transformer & Technical Indicators

This project aims to build a **Machine Learning model** using the **Transformer architecture** to predict `Buy / Hold / Sell` signals for Thai stocks. The model leverages **OHLCV data**, **Tick data**, and **technical indicators** to help identify precise entry and exit points for trading decisions.

---

## Workflow

### Step 1: Load Stock Data from MySQL
- Use `pymysql` to load daily stock data from the `stock_hloc` table  
- Use SQL queries to extract tick-level trading data from the `stocksm_tickmatchs` table  
- Merge OHLCV and Tick data for unified analysis

---

### Step 2: Compute Technical Indicators
The `compute_indicators(df)` function calculates a wide range of technical indicators including:
- **RSI**, **MACD**, **PPO**, **ADX**, **ATR**, **StochRSI**, and more

---

### Step 3: Generate Labels using N-Period Min-Max (NPMM)
Labels are assigned based on future price behavior within a 15-day window:
- `Buy` → If the current price is the **lowest** in the next 15 days  
- `Sell` → If the current price is the **highest** in the next 15 days  
- `Hold` → All other cases

---

### Step 4: Create Sequential Data (X, y)
- Use a **15-day sliding window** to generate time-series sequences  
- Include over **35 features** derived from OHLCV and Tick data such as:
  - `Buy/Sell Volume Ratio`
  - `Bearish Engulfing`
  - `Buy Dominance`
  - `Long Upper Shadow`
  - And more...

---

### Step 5: Build and Train Transformer Model
- Implement a **Transformer-based model** using **PyTorch**
- Handle class imbalance with **SMOTE** or **RandomOversampling**
- Use **CrossEntropy Loss** (with optional label smoothing)
- Evaluate model performance with:
  - Accuracy  
  - F1-Score  
  - Confusion Matrix  
  - Feature Importance

---

### Step 6: Training & Backtesting
- Apply **Walk-Forward Expanding Window** strategy for model training
- Save the best model and label encoder
- Perform **backtesting** on real daily stock data:
  - Predict and overlay signals on price charts  
  - Compute **cumulative returns**  
  - Visualize performance using graphs  
  - Analyze signal statistics per stock (Buy / Hold / Sell)
