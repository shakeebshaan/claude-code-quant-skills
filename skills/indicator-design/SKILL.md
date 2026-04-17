---
name: indicator-design
description: Interactive skill for designing custom technical indicators from a trading hypothesis. Claude asks about the hypothesis, helps operationalize it, and generates vectorized pandas implementations. Use when starting from a discretionary trading idea and wanting to test it systematically.
---

# Indicator Design Skill

Help the user turn a discretionary trading hypothesis into a vectorized, testable indicator.

## Flow

### 1. Extract the hypothesis
Ask:
- What pattern are you trying to capture in one sentence?
- When is the pattern "on"? What market condition?
- What is the expected return when the pattern fires?
- Roughly how often does it fire historically?

### 2. Operationalize
Convert verbal description to measurable quantities:
- "High volume" → `volume > rolling_quantile(volume, 90, window=20)`
- "Breakout" → `close > rolling_max(high, window=N).shift(1)`
- "Exhaustion" → `rsi(close, 14) > 80 and rsi(close, 14).shift(1) > 80`

Push back on fuzzy language. Ask for concrete thresholds.

### 3. Implement — vectorized, no loops

```python
import pandas as pd

def indicator(df: pd.DataFrame, window: int = 20, quantile: float = 0.9) -> pd.Series:
    """One-liner describing what this indicator outputs (signal series, bool series, score)."""
    # implementation — pure pandas, no .apply, no Python loops
    ...
    return signal
```

Rules:
- Pure pandas operations. No `for` loops over bars.
- Return `pd.Series` aligned with input index.
- Use `.shift(1)` for any rolling computation used at decision time — no look-ahead.
- Document whether the signal is {-1, 0, 1}, probability [0, 1], or raw score.

### 4. Validate
Before the user tests it, check:
- Does the signal fire at the expected frequency?
- Is it well-distributed across regimes?
- Does it correlate with known factors (trend, vol, momentum)?

Generate this sanity-check code:

```python
print(f"fires: {signal.sum()} of {len(signal)} bars ({signal.mean():.2%})")
print(f"by year: {signal.groupby(df.index.year).mean()}")
print(f"corr with returns: {signal.corr(df['close'].pct_change().shift(-1))}")
```

### 5. Next step
Suggest the user feed the signal to `backtest-review` skill next.

## Anti-patterns to reject

- "Use ML to detect the pattern" — no. Operationalize first, test edge, THEN consider ML.
- "Let the algorithm find the best threshold" — no. Threshold per hypothesis, test out-of-sample.
- Indicators with >3 parameters — push back, ask to simplify.
- Indicators combining >4 conditions with AND — almost guaranteed overfit; ask what each condition adds.
