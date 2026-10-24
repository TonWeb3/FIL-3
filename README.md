# Polymarket BTC 15m Assistant [Apex Baseline]
### Stage 0: Apex Baseline (No Protections)

A real-time, high-performance automated trading bot for Polymarket **"Bitcoin Up or Down" 15-minute** binary options markets, executing via **Good 'Til Date (GTD)** limit orders powered by **Stage 0: Apex Baseline** from the 7,678-market optimization report.

---

## 🏆 Empirical Backtest Reference (80.1% Win Rate Benchmark)

Evaluated across **7,678 historical Polymarket BTC 15-minute markets**:
- **Rank**: **#3 Overall** (out of 30 simulated strategy configurations)
- **Win Rate**: **80.1%** (2,577 Wins / 640 Losses across 3,217 executed trades)
- **Market Coverage**: **41.9%** of all available 15m markets
- **Profit Factor**: **1.40**
- **Net Profit**: **+$233,547,203,358,014.25**
- **Annualized Sharpe Ratio**: **4.00**
- **Directional Symmetry**:
  - **UP Trades**: 1,592 trades | **80.5% Win Rate**
  - **DOWN Trades**: 1,625 trades | **79.8% Win Rate**

---

## 🧠 Hardcoded Quantitative Strategy Architecture

### 1. Alpha & Entry Rules (`decide_ev`)
- **Continuous EV Latency Arbitrage Engine**: Evaluates closed-form Geometric Brownian Motion (GBM) fair probability from live Binance spot ticks against Polymarket CLOB book best asks ($EV \ge 0.04$, min prob $0.55$).
- **Window Timing Envelope ($90\text{s} \le t \le 600\text{s}$)**:
  - **Skips the first 90 seconds (Minute 0 noise)**: Avoids wide opening spreads and strike discovery noise.
  - **Skips after Minute 10 ($t > 600\text{s}$)**: Avoids buying inflated 85¢–95¢ contracts where a single late reversal destroys multiple wins.
- **Strike Moneyness Margin of Safety (`min_strike_distance_bp: 6.0`)**:
  - Requires Bitcoin to have moved at least **6.0 basis points** ($0.06\% \approx \$60$ on BTC) away from the opening strike in the direction of the trade:
    - **UP**: Spot must be $\ge +6.0\text{ bp}$ above strike.
    - **DOWN**: Spot must be $\le -6.0\text{ bp}$ below strike.
  - Eliminates 56% of coin-flip trades in the near-the-money chop zone.
- **15m Structural Heikin-Ashi Alignment (`use_15m_ha: True`)**:
  - Prohibits betting DOWN when 15m HA has a flat bottom.
  - Prohibits betting UP when 15m HA has a flat top.

### 2. Pure Baseline Architecture (No Protections)
- **NO Circuit Breakers**: Trades every valid signal without pausing for cooldowns.
- **NO Volatility Gates**: Trades through all ATR turbulence and news releases.
- **NO Skip Hours**: Operates 24/7 across all UTC hours.
- **Micro-Vetoes DISABLED (`use_vetoes: false`)**.

---

## ⚡ GTD Low-Latency Execution Engine

- **Order Type**: Good 'Til Date (`OrderType.GTD`) limit orders with a **5-minute expiration timestamp**.
- **Signing Speed**: Sub-0.1ms C-accelerated EIP-712 signing via `coincurve`.
- **Pre-Warmed Token Caches**: Pre-fetched token IDs for instant zero-REST execution.
- **Reactive L2 WebSocket Feed**: Instant event-driven trade evaluation.
- **Default Port**: **`8087`**.

---

## 🏃 Running the Bot

```powershell
python main.py
```

Dashboard access:
👉 **`http://localhost:8087`**
