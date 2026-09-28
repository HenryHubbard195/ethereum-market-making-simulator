# ETH Market-Making Simulator

A Python-based simulation of an **Ethereum (ETH) market-making strategy**, designed to explore how quote placement, spread capture, inventory management, and price volatility interact to affect market-maker P&L.

The simulator models a market maker continuously quoting a bid and ask around a simulated ETH price, executing randomly arriving orders while dynamically adjusting quotes based on current inventory.

## Overview

The simulation combines:

* **Geometric Brownian Motion-style price simulation** for ETH
* Dynamic bid/ask spreads based on simulated price volatility
* **Inventory-based quote skew**
* Maximum inventory constraints
* Randomised incoming buy and sell orders
* Spread-capture and mark-to-market P&L decomposition
* Maximum drawdown and Sharpe ratio calculations
* Visualisation of price, quotes, inventory and P&L

The objective is to investigate how a market maker can balance **spread capture against inventory risk**.

## Strategy

### 1. ETH Price Simulation

The ETH mid-price is simulated using a simple stochastic process:

```text
P(t+1) = P(t) × (1 + drift + volatility × ε)
```

where `ε` is sampled from a standard normal distribution.

The default simulation uses:

```python
initial_price = 3500.0
volatility = 0.01
drift = 0.0
steps = 5000
```

This provides a controlled environment for testing the market-making logic.

### 2. Dynamic Bid/Ask Quotes

Rather than using a fixed spread, the simulator scales the half-spread with the dollar magnitude of the simulated price movement:

```text
half_spread = max(min_spread, spread_multiplier × price × volatility)
```

This means quoted spreads increase as simulated volatility increases.

### 3. Inventory Skew

Quotes are adjusted according to the market maker's current ETH inventory.

When inventory becomes positive, the strategy shifts quotes to make selling ETH more attractive. When inventory becomes negative, quotes shift in the opposite direction.

The inventory adjustment is:

```text
inventory_adjustment = inventory_skew × inventory × |inventory|
```

This creates a non-linear response to increasing inventory exposure.

### 4. Inventory Risk Limits

The simulator imposes a maximum inventory:

```python
max_inventory = 3.0
```

Orders that would cause inventory to exceed this limit are
