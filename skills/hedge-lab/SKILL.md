---
name: hedge-lab
description: Explore hedging constructs for a crypto position — delta hedging, basis trading, funding-rate capture, cross-exchange arb. Use when holding directional BTC/ETH exposure and wanting to reduce risk without closing the position.
---

# Hedge Lab Skill

Help the user evaluate hedging constructs for their crypto exposure. Each construct has different cost, complexity, and residual risk profiles.

## Ask first

1. What is the exposure? (e.g. long 10 BTC spot, short 100 ETH perp)
2. What is the goal? (delta-neutral / reduce tail risk / capture funding / earn basis)
3. What is the holding period? (overnight / weeks / structural)
4. Exchanges available? (Binance, OKX, Deribit, Bybit, on-chain)
5. Capital and leverage constraints?

## Constructs — walk through each relevant one

### 1. Delta hedging with perpetual futures
- Short perp against spot → flat delta
- Earnings: funding rate when positive (longs pay shorts)
- Risks: funding inversion (shorts pay longs), basis move, forced liquidation if margin underfunded
- Capital efficiency: 1x margin on perp typically, so 50% of spot notional

### 2. Spot–perp basis trading
- Long spot + short dated futures
- Earnings: annualized basis, typically 2–15% on BTC
- Risks: basis widening before expiry (mark-to-market loss), funding (if perp instead of dated)

### 3. Funding rate capture
- Neutral position designed purely to collect funding
- Spot long + perp short (if funding > 0)
- Spot short (via borrow) + perp long (if funding < 0)
- Earnings: sum of funding payments
- Risks: borrow cost, funding can invert, slippage on entry/exit

### 4. Options — protective put
- Long spot + long OTM put (e.g. 15% OTM, 30d expiry)
- Earnings: none (pure cost)
- Protection: unlimited downside beyond strike
- Risks: premium cost, expiry timing, IV regime

### 5. Collar
- Long spot + long put + short call
- Earnings: premium from call offsets put cost (zero-cost collar if delta-matched)
- Caps upside above call strike
- Good for structural hedging of a long position you want to keep

### 6. Cross-exchange arbitrage
- Price discrepancy between exchanges → long on cheap venue, short on expensive
- Earnings: convergence of spread
- Risks: withdrawal latency, custody risk on both venues, slippage

### 7. Delta-neutral LP / market making
- Concentrated LP + directional hedge via perps
- Earnings: LP fees minus hedge cost
- Risks: impermanent loss model breakdown, perp basis divergence

## For each construct, output

- **Cost estimate** (bps per day / per month)
- **Tail exposure remaining**
- **Operational complexity** (1–5, how many moving parts)
- **Minimum capital to run efficiently**
- **When this is the right choice** — specific regime / holding-period / risk-profile

## Output format

Start with a 1-sentence "best fit" recommendation given their stated exposure + goal.
Then table of top 3 constructs with the columns above.
Then detail on #1 choice: exact implementation, position sizing, monitoring checklist.

Never recommend more leverage than the user stated capital supports. Always mention the liquidation price / margin buffer.
