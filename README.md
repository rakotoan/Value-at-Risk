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

### 1. Data Collection and Preparation

The notebook downloads daily market prices using `yfinance`.


### 2. Daily Log Returns

For each asset, daily log returns are calculated as:

```math
R_{i,t} = \ln\left(\frac{S_{i,t}}{S_{i,t-1}}\right)
```

where:

- $`S_{i,t}`$ is the price of asset $`i`$ on day $`t`$;
- $`R_{i,t}`$ is its daily log return.

The historical sample is used to estimate the mean vector $`\mu`$ and covariance matrix $`\Sigma`$.

### 3. Monte Carlo Simulation

The notebook generates 10,000 joint asset-return scenarios:

$$
R^{(s)} \sim \mathcal{N}(\mu,\Sigma)
$$

The covariance matrix determines the dependence between asset returns within each scenario.

```python
rng = np.random.default_rng()

simulated_returns = rng.multivariate_normal(
    mean=mu,
    cov=sigma,
    size=MC
)
```

No random seed is fixed in the current notebook, so simulation results vary between runs.

### 4. Simulated Prices and Portfolio P&L

For each scenario, asset prices are obtained from the simulated log returns:

$$
S_i^{(s)} = S_{i,0}\exp\left(R_i^{(s)}\right)
$$

Initial asset quantities are calculated as:

$$
q_i = \frac{V_0 w_i}{S_{i,0}}
$$

The simulated portfolio value and P&L are then:

$$
V^{(s)} = \sum_{i=1}^{N} q_i S_i^{(s)}
$$

$$
P\&L^{(s)} = V^{(s)} - V_0
$$

### 5. Monte Carlo VaR

The 5th percentile of simulated P&L is:

$$
q_{0.05} = \operatorname{Quantile}_{0.05}(P\&L)
$$

VaR is reported using the opposite sign:

$$
\mathrm{VaR}_{95\%,1d}^{MC} = -q_{0.05}
$$

```python
pnl_quantile_5 = np.quantile(PnL, 0.05)
var_95_mc = -pnl_quantile_5
```

### 6. Parametric Approximation

The mean and variance of the weighted asset log-return sum are:

$$
\mu_p = w^\top \mu
$$

$$
\sigma_p^2 = w^\top \Sigma w
$$

where $w$ is the allocation vector and $\Sigma$ is the covariance matrix of daily asset log returns.

Using the first-order approximation of simple returns by log returns, the normal parametric VaR is:

$$
\mathrm{VaR}_{95\%,1d}^{param}
=
-V_0\left(\mu_p+\Phi^{-1}(0.05)\sigma_p\right)
$$

Since $\Phi^{-1}(0.05)\approx-1.645$, this is equivalent to:

$$
\mathrm{VaR}_{95\%,1d}^{param}
\approx V_0\left(1.645\sigma_p-\mu_p\right)
$$

```python
mu_p = allocation @ mu
sigma_p = np.sqrt(allocation @ sigma @ allocation)

var_95_parametric = -initial_portfolio_value * (
    mu_p + norm.ppf(0.05) * sigma_p
)
```

The Monte Carlo method exponentiates each asset's simulated log return before valuing the portfolio. The parametric method instead uses a linear approximation. Their estimates therefore need not match exactly, even with a large number of scenarios.

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

Before interpreting the parametric comparison, apply the sign correction described above.
