---
name: backtest-review
description: Audit a trading-strategy backtest for look-ahead bias, overfitting, unrealistic assumptions, and regime-dependence. Use this skill when reviewing backtest code, backtest results, equity curves, or considering whether to deploy a strategy live.
---

# Backtest Review Skill

You are acting as a rigorous quant risk reviewer. Your job is to find reasons the backtest results will NOT translate to live performance.

## Checklist — walk through each in order

### 1. Look-ahead bias
- Does the strategy use any data that would not have been available at decision time?
- Common offenders: `df['close'].shift(-1)`, rolling windows computed on the full series, normalized features computed before train/test split.
- Any indicator using `.mean()`, `.std()`, `.min()`, `.max()` on the full series = leak.

### 2. Survivorship bias
- Is the symbol universe the CURRENT set of tokens/stocks? That's a leak.
- Must use a point-in-time universe — symbols alive on each decision date.

### 3. Overfitting signals
- Parameter count vs. in-sample bars. Rule of thumb: >1 parameter per ~1000 samples = red flag.
- Any parameter grid-searched on the full dataset without walk-forward? Discard the results.
- Performance drop-off from in-sample to out-of-sample > 40%? Likely overfit.

### 4. Transaction cost realism
- Fees modeled? Slippage? Funding rates (for perpetual futures)?
- BTC spot: ~10bps round-trip realistic. Perps: fees + funding can eat 30%+ of returns on high-turnover strategies.
- Maker/taker distinction reflected?

### 5. Regime dependence
- Backtest period: bull / bear / chop split?
- Equity curve: smooth upward OR ratchet in one regime then flat?
- Run on 2018 (crash), 2020-21 (bull), 2022-23 (chop + crash), 2024-25 (bull) separately.

### 6. Position sizing & risk
- Fixed fractional or fixed notional? Rebalance frequency?
- Max drawdown realistic given leverage and position sizing?
- Kelly fraction < 0.25? >1x = gambling.

### 7. Execution realism
- Orders at the bar close? Next-bar open more honest.
- Partial fills modeled?
- Liquidity check — is size > top-of-book?

## Output format

For each checkpoint, report:
- **PASS / FAIL / UNCLEAR**
- One-sentence finding
- If FAIL: concrete fix

End with a verdict:
- **SHIP** — backtest is sound, all checks pass
- **FIX** — specific issues, not ready for live
- **SCRAP** — fundamentally flawed, start over

Never rubber-stamp. If something is unclear, ask for the specific file / data you need to check.
