# Portfolio Value at Risk — Monte Carlo and Parametric Approach

This project estimates the one-day Value at Risk (VaR) of a multi-asset portfolio using Monte Carlo simulation and compares it with a normal parametric approximation.

---

## ⚙️ Working Conditions

- Initial capital: $1,000,000.
- Risk horizon: one trading day.
- Confidence level: 95%.
- Simulations: 10,000.
- Historical data: daily prices from January 2010 to the execution date, subject to data availability.
- Data source: Yahoo Finance, accessed through `yfinance`.
- Return definition: daily logarithmic returns.
- Simulation assumption: asset log returns follow a multivariate normal distribution.
- Positions: fixed asset quantities over the one-day horizon.

---

## 🎯 Goal

Estimate the portfolio's downside risk over one trading day by:

- Simulating correlated asset returns.
- Converting simulated returns into prices and portfolio values.
- Extracting the 5th percentile of the portfolio P&L distribution.
- Comparing the Monte Carlo estimate with a linear normal approximation.

VaR at a 95% confidence level is a loss threshold with an estimated 5% probability of being exceeded over the specified horizon, under the model assumptions. It is not the maximum possible loss.

---

## 🧠 Methodology

### 1. Historical Data and Returns

The notebook downloads daily prices using `yfinance`, removes missing observations, and calculates daily log returns. Historical returns are used to estimate each asset’s mean return and the covariance matrix.

### 2. Monte Carlo Simulation

The model generates 10,000 correlated one-day log-return scenarios using a multivariate normal distribution. No random seed is fixed, so simulation results vary between runs.

### 3. Portfolio Valuation and P&L

Simulated log returns are converted into asset prices. Each scenario is valued using the initial asset quantities, which remain fixed over the one-day horizon. Portfolio P&L is calculated as the simulated portfolio value minus initial capital.

### 4. Monte Carlo VaR

The model extracts the 5th percentile of the simulated P&L distribution and reverses its sign to report the one-day VaR at 95% confidence as a loss amount.

### 5. Parametric Comparison

The Monte Carlo estimate is compared with a normal parametric approximation based on portfolio weights, historical mean log returns, and the covariance matrix.

The parametric method uses a linear approximation, while the Monte Carlo method converts each simulated log return into a price before valuing the portfolio. The two estimates therefore need not match exactly.

---

## 📊 Results

<img width="850" height="470" alt="image" src="https://github.com/user-attachments/assets/770c6cf0-36a4-40eb-bd26-92fffb7fff63" />

---

## ⚙️ Prerequisites

- Python 3.
- Jupyter Notebook.
- Internet access to retrieve market data.
- Python packages: `numpy`, `pandas`, `yfinance`, `scipy`, and `matplotlib`.

---

## 📌 Installation

Install the required packages:

```bash
pip install numpy pandas yfinance scipy matplotlib notebook
```

Alternatively, if the repository includes a matching `requirements.txt`:

```bash
pip install -r requirements.txt
```

---

## 💻 Usage

Start Jupyter Notebook:

```bash
jupyter notebook
```

Then open:

```text
VaR.ipynb
```

Run the cells sequentially to download prices, estimate return parameters, simulate portfolio P&L, and calculate VaR.
