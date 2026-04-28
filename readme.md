# VPIN-Based Market Fragility Detection in BTCUSDT

## Overview

This project evaluates the **Volume-synchronized Probability of Informed Trading (VPIN)** as an indicator of short-term downside risk in BTCUSDT using high-frequency Binance trade data.

Using a six-month dataset (May–October 2025), we construct volume-time buckets and systematically test VPIN parameter configurations to assess:

* Forward return behavior after high VPIN
* Crash probability conditioning
* Stability across time
* Robustness across parameter choices

The results show that **elevated VPIN consistently identifies fragile market regimes**, characterized by negative forward returns and increased downside risk.

---

## Data

* Source: Binance BTCUSDT aggregated trades (`aggTrades`)
* Period:
  **2025-05 → 2025-10**
* Sampling: Trade-level data aggregated into **volume-based buckets**

---

## Methodology

### Volume Bucketing

Instead of time bars, we use **volume-time sampling**:

* Base bucket size: **25 BTC**
* Aggregated bucket sizes tested:

  ```
  V ∈ {25, 50, 100, 200} BTC
  ```

Each bucket tracks:

* Buy volume
* Sell volume
* Price (last trade)
* Time interval

---

### VPIN Construction

For each bucket:

[
\text{Imbalance}_i = \frac{|V^{buy}_i - V^{sell}_i|}{V}
]

VPIN is defined as:

[
\text{VPIN}*t = \frac{1}{n} \sum*{i=1}^{n} \text{Imbalance}_i
]

Rolling window:

```
n ∈ {50, 75, 100}
```

---

### Targets

Forward log returns:

```
15 min, 30 min, 60 min
```

Crash definition:

```
15m: ≤ -0.7%
30m: ≤ -1.0%
60m: ≤ -1.5%
```

---

## Grid Search

We evaluate combinations of:

```
V ∈ {25, 50, 100, 200}
n ∈ {50, 75, 100}
horizon ∈ {15m, 30m, 60m}
```

Metrics:

* Information Coefficient (IC)
* Rank IC
* Mean return conditional on top 5% VPIN
* Crash probability
* Crash lift (relative to baseline)
* Classification metrics (precision / recall / F1)

---

## Key Results

### Best Parameter (selected)

```
V = 50 BTC
n = 100
```

---

## 1. Event Study (Core Result)

Time-aligned event study after VPIN spikes (top 5%):

| Minutes | Mean Return       |
| ------- | ----------------- |
| 1m      | ~0                |
| 5m      | slightly negative |
| 10m     | -0.02%            |
| 15m     | -0.04%            |
| 30m     | -0.07%            |
| 60m     | -0.11%            |

### Interpretation

> High VPIN is followed by **persistent negative return drift**, especially over 15–60 minute horizons.

---

## 2. Crash Probability

For the best configuration:

### 30-minute horizon

* Baseline crash probability: ~1.2%
* High VPIN (top 5%): ~2.6%
* **Crash lift ≈ 2.1×**

### 60-minute horizon

* Baseline: ~0.8%
* High VPIN: ~2.6%
* **Crash lift ≈ 3.3×**

### Interpretation

> Elevated VPIN identifies periods with significantly higher downside risk.

---

## 3. Signal Strength

Representative metrics:

* IC (30m): ≈ **-0.06**
* IC (60m): ≈ **-0.07**
* Top 5% VPIN return: negative across horizons

### Interpretation

> VPIN shows **consistent negative correlation with forward returns**, strongest at longer horizons.

---

## 4. Distribution Shift

Comparing return distributions:

* High VPIN → heavier left tail
* Lower mean return
* Increased probability of extreme negative moves

---

## 5. Stability

Month-by-month analysis shows:

* Directional consistency (negative returns after high VPIN)
* Some variability in magnitude
* Stronger effects during volatile periods

### Interpretation

> The signal is **structurally stable**, though intensity varies by regime.

---

## 6. Visualization Highlights

The notebook generates:

* Price + VPIN overlay
* Volume bucket duration (log scale)
* Time-aligned event study
* Return distribution (high vs normal VPIN)
* Crash probability lift
* Month-by-month stability
* Spike-window case studies

---

## Key Insight

> **VPIN functions as a market fragility indicator.**

* High VPIN → order-flow imbalance → stressed liquidity
* These regimes are followed by:

  * weaker returns
  * higher probability of downside events

---

## Takeaway

* VPIN is **not a precise timing signal**
* It is a **state variable** indicating:

  ```
  “the market is fragile”
  ```
* It is most useful for:

  * risk monitoring
  * regime detection
  * contextualizing short-term market conditions

---

## Project Structure

```
data/
    BTCUSDT-aggTrades-YYYY-MM.csv

notebook/
    vpin_combined_grid_plots_clean.ipynb

outputs/
    cache/
    tables/
    figures/
```

---

## Reproducibility

Run the notebook:

```
vpin_combined_grid_plots_clean.ipynb
```

It will:

1. Build monthly base buckets (memory-safe)
2. Cache intermediate results
3. Run grid search
4. Generate tables + plots

---

## Future Work

* Sub-minute horizons (1s–60s)
* Regime-switching models
* Cross-asset validation (futures, equities)
* Execution / trading applications

---

## Summary

> VPIN provides a structured way to detect **order-flow-driven market stress**, and high VPIN regimes consistently precede weaker returns and elevated downside risk in BTCUSDT.
