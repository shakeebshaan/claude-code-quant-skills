# claude-code-quant-skills

> Claude Code skills, slash commands, and hooks tuned for quant research, trading-strategy analysis, and crypto backtesting workflows.

[![Stars](https://img.shields.io/github/stars/shakeebshaan/claude-code-quant-skills?style=flat-square&color=yellow)](https://github.com/shakeebshaan/claude-code-quant-skills/stargazers)
[![Forks](https://img.shields.io/github/forks/shakeebshaan/claude-code-quant-skills?style=flat-square&color=blue)](https://github.com/shakeebshaan/claude-code-quant-skills/network/members)
[![Last Commit](https://img.shields.io/github/last-commit/shakeebshaan/claude-code-quant-skills?style=flat-square&color=green)](https://github.com/shakeebshaan/claude-code-quant-skills/commits/main)
[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg?style=flat-square)](LICENSE)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-compatible-7C3AED?style=flat-square)](https://docs.anthropic.com/claude-code)

**By [@shakeebshaan](https://github.com/shakeebshaan)** · MIT license · contributions welcome

---

## What this is

A collection of [Claude Code](https://docs.anthropic.com/claude-code) skills you can drop into `~/.claude/skills/` to supercharge quantitative research and trading workflows.

Each skill is a focused prompt + instruction set that teaches Claude how to approach a specific quant task — backtesting, indicator design, risk analysis, strategy review — with the rigor the domain demands.

## Skills

### `/backtest-review`
Walks Claude through a structured audit of a backtest:
- look-ahead bias
- survivorship bias
- overfitting signals (train/test split, parameter count vs. sample size)
- transaction-cost realism
- slippage model
- regime-dependence

### `/strategy-critique`
Adversarial review of a trading strategy. Claude acts as a skeptical prop-desk risk manager and tries to find why the strategy will blow up.

### `/indicator-design`
Interactive skill for designing custom technical indicators. Claude asks about the hypothesis, helps you operationalize it, and generates vectorized pandas implementations.

### `/risk-report`
Generates a one-page risk summary from a returns series: Sharpe, Sortino, max DD, CVaR, tail ratio, rolling correlation to BTC/SPY.

### `/hedge-lab`
Explores hedging constructs for a position: delta hedging, basis trading, funding-rate capture, cross-exchange arbitrage. Claude walks through pros/cons and risk of each.

### `/data-scrub`
Audits a market-data CSV for common issues: missing bars, timezone drift, weekend handling, stale ticks, duplicate timestamps, survivorship in historical symbol lists.

## Install

```bash
# clone this repo
git clone https://github.com/shakeebshaan/claude-code-quant-skills.git

# symlink the skills dir into your Claude config
ln -s "$(pwd)/claude-code-quant-skills/skills" ~/.claude/skills/quant
```

Then in Claude Code: `/backtest-review` — autocompletes.

## Structure

```
skills/
├── backtest-review/SKILL.md
├── strategy-critique/SKILL.md
├── indicator-design/SKILL.md
├── risk-report/SKILL.md
├── hedge-lab/SKILL.md
└── data-scrub/SKILL.md
hooks/
└── pre-commit-backtest-check.sh
```

## Why

Quant workflows are high-stakes and boring to do manually. LLMs without guardrails hallucinate numbers, miss look-ahead bias, and produce confident nonsense. These skills encode domain judgment so Claude pushes back, asks the right questions, and produces output you can actually ship.

## Contributing

Add a skill as a new directory under `skills/<name>/SKILL.md`. Keep skills narrow — one skill, one job. Include a frontmatter block describing triggers.

## License

MIT — see [LICENSE](LICENSE).

## See also

- [shakeebshaan/awesome-crypto-trading-agents](https://github.com/shakeebshaan/awesome-crypto-trading-agents) — curated list of OSS trading bots + agent frameworks
- [shakeebshaan/btc-hedge-lab](https://github.com/shakeebshaan/btc-hedge-lab) — hedging strategies research playground
