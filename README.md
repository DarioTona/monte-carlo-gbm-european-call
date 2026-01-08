# Monte Carlo Simulation of Geometric Brownian Motion: AAPL Forecasting and European Call Pricing

In this notebook we simulate future price paths for **Apple Inc. (AAPL)** using a **Geometric Brownian Motion (GBM)** model calibrated from historical **daily log returns**. The simulated terminal price distribution over a **6-month horizon** is then used to price a **European call option** via Monte Carlo.

## Log Returns, Mean and Volatility Estimation

We work with **daily log returns** defined as:

$$
r_t = \ln\left(\frac{S_t}{S_{t-1}}\right)
$$

where $S_t$ is the adjusted close price at day $t$.  
From the log-return series $\{r_t\}$ we estimate the **sample mean** $\hat{\mu}$ and the **sample volatility** $\hat{\sigma}$ (standard deviation), which will be used to calibrate the GBM model. To express them on an **annual** basis (using the standard convention of $252$ trading days per year), we apply:

$$
\mu_{\text{ann}} = 252 \cdot \mu_{\text{daily}}
$$

$$
\sigma_{\text{ann}} = \sqrt{252} \cdot \sigma_{\text{daily}}
$$
## Geometric Brownian Motion (GBM)

We model the stock price $S_t$ with a **Geometric Brownian Motion**.  
Under GBM, the price dynamics are given by:

$$
dS_t = \mu S_t \, dt + \sigma S_t \, dW_t
$$

where $\mu$ is the drift (expected return), $\sigma$ is the volatility, and $W_t$ is a standard Brownian motion.

In discrete time, using a time step $dt$ (in years), the GBM solution can be simulated as:

$$
S_{t+dt} = S_t \exp\left[\left(\mu - \frac{1}{2}\sigma^2\right)dt + \sigma\sqrt{dt}\,Z\right]
$$

with $Z \sim \mathcal{N}(0,1)$.


$$

<img width="1000" height="541" alt="image" src="https://github.com/user-attachments/assets/61bf1fe3-8b88-4e2a-882a-3a1812f7a044" />

## European Call Option Pricing 

A key goal of the analysis is to study the **convergence of the Monte Carlo estimator**: we compute the call price for increasing numbers of simulations $N$ and track how the estimate stabilizes as $N$ grows, together with its **standard error** and confidence bands. This provides an empirical illustration of the typical Monte Carlo behavior, where the estimation error decreases approximately at the rate $O(N^{-1/2})$.

We price a **European call option** with payoff:

$$
\text{Payoff} = \max(S_T - K, 0)
$$

Instead of simulating the full path, we simulate the terminal price directly using the closed-form **GBM** solution:

$$
S_T = S_0 \exp\left(\left(r-\tfrac{1}{2}\sigma^2\right)T + \sigma\sqrt{T}\,Z\right),
\qquad Z \sim \mathcal{N}(0,1)
$$

Under the **risk-neutral measure**, the option price is the discounted expected payoff:

$$
C_0 = e^{-rT}\\mathbb{E}\left[\max(S_T - K, 0)\right]
$$

To be consistent with Black–Scholes / GBM pricing, we simulate $S_T$ using the **risk-free rate** $r$ as drift (risk-neutral drift), while keeping the volatility $\sigma$ estimated from historical data.

With $N$ Monte Carlo simulations, the estimator becomes:

$$
\hat{C}_0(N) = e^{-rT}\\frac{1}{N}\sum_{i=1}^N \max\left(S_T^{(i)}-K,0\right)
$$

## Standard error and confidence interval

For each $N$ in a grid (increasing sample sizes), we compute the estimate $\hat{C}_0(N)$ and the Monte Carlo **standard error**:

$$
SE(N) = e^{-rT}\\frac{s}{\sqrt{N}}
$$

where $s$ is the sample standard deviation of the payoff.

Using the Central Limit Theorem, we also report an approximate 95% confidence interval:

$$
\hat{C}_0(N) \pm 1.96\,SE(N)
$$

<img width="994" height="544" alt="image" src="https://github.com/user-attachments/assets/2d8f4d3c-885f-4856-b58c-aecc41011c3f" />


Note: in the **forecast / comparison with the realized path** we calibrate the model using **historical returns** (physical/real-world measure). For **option pricing**, we switch to the **risk-neutral** drift (the risk-free rate $r$).





