---
name: data-scrub
description: Audit a market-data CSV for missing bars, timezone drift, weekend handling, stale ticks, duplicate timestamps, and survivorship. Use before running any backtest on new data.
---

# Data Scrub Skill

Garbage in → backtest lies. Before ANY strategy runs on new data, audit it.

## Ask for

- File path
- Expected timeframe (1m, 5m, 1h, 1d)
- Expected timezone (UTC recommended)
- Expected date range
- Symbol(s) covered

## Run these checks — each must PASS

### 1. Schema
- Required columns: `timestamp, open, high, low, close, volume`
- Dtypes correct? (timestamp = datetime64, OHLCV = float)

### 2. Timestamp integrity
- Monotonic increasing, no duplicates
- No gaps greater than 1 bar (for 24/7 crypto) or expected gaps (market hours for equities)
- Timezone explicit and consistent — never naive
- No daylight-saving jumps

### 3. Bar integrity
- `low <= open <= high` for every bar
- `low <= close <= high` for every bar
- `low <= high` (sanity)
- `volume >= 0`

### 4. Missing data
- Count NaN in each column
- Flag any bar where volume = 0 AND OHLC all equal → likely a stale / synthetic bar
- Flag bars where `high == low` with non-zero volume → suspicious

### 5. Outliers
- Return series: count bars with |return| > 20% (flag for review)
- Volume spikes > 20x 30-bar rolling median → real or data error?

### 6. Symbol-level (if multi-symbol)
- Symbol universe consistent across time, or point-in-time?
- Delistings handled?
- Ticker changes / splits adjusted?

### 7. Corporate actions (equities)
- Dividend-adjusted?
- Split-adjusted?
- If not, warn user before using for backtests.

## Code template

```python
import pandas as pd

def scrub(df: pd.DataFrame, timeframe: str = "1h") -> dict:
    issues = {}
    expected_delta = pd.Timedelta(timeframe)

    issues["dupes"]       = df.index.duplicated().sum()
    issues["monotonic"]   = df.index.is_monotonic_increasing
    issues["gaps"]        = ((df.index.to_series().diff() > expected_delta).sum())
    issues["ohlc_break"]  = ((df["low"] > df["open"]) |
                             (df["low"] > df["close"]) |
                             (df["high"] < df["open"]) |
                             (df["high"] < df["close"]) |
                             (df["low"] > df["high"])).sum()
    issues["neg_volume"]  = (df["volume"] < 0).sum()
    issues["nan_rows"]    = df.isna().any(axis=1).sum()
    issues["stale_bars"]  = ((df["volume"] == 0) &
                             (df["open"] == df["close"])).sum()
    return issues
```

## Output format

Single markdown table with Check / Result / Severity / Fix.

Severity:
- **BLOCK** — do not backtest until fixed
- **WARN** — acknowledge and document
- **OK** — passed

End with a one-sentence verdict: `ready for backtest` / `fixes required`.
