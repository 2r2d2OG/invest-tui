# invest-tui — Real-time investment assistant in your terminal

invest-tui is a lightweight terminal application (TUI) written in Rust that monitors markets in real time, detects favorable entry points, and helps you plan regular investments. It is built for long-term European investors who use dollar-cost averaging (DCA), with first-class support for Trade Republic's fee structure.

## Features

**Live market data, for free.** Crypto prices stream through Binance's public WebSocket with automatic reconnection and exponential backoff. Stocks and ETFs (Euronext, Xetra, US markets) are polled from Yahoo Finance only during trading hours, with timezone and DST handling and rate-limit protection. Finnhub and Alpha Vantage can be used as fallbacks.

**Institutional-grade timing engine.** Indicators are computed locally in pure Rust: RSI, EMA 50/200, MACD, Bollinger Bands, ATR, realized volatility, rolling VWAP, and Anchored VWAP on impulsive moves. A 0–100 opportunity score combines them into one of three signals, Strong Buy, DCA or Wait, with hysteresis, confirmation rules and falling-knife guards. For daily candles, indicators use closed candles only, so incomplete intraday volume never distorts the analysis. Assets can be analyzed on their reference futures contract (CME, NYMEX, COMEX), with automatic rollover back-adjustment and a spot bridge for crypto.

**Smart allocation.** Enter the cash you want to invest and invest-tui selects a few assets that fit your budget: your core ETF plus the best current opportunities. It splits the amount between immediate orders and scheduled savings plans, accounting for broker fees.

**AI advisor.** A built-in chat panel streams answers from OpenAI, Anthropic, Gemini or a local Ollama model. Each question injects your investor profile, market indicators and aggregated portfolio data. Raw statements and transaction details never leave your machine.

**Portfolio tracking.** Log purchases from the terminal, import Trade Republic PDF/CSV statements fully offline using configurable regex rules, track your average cost basis (fees included), and set take-profit alerts.

## Getting started

Requirements: Rust 1.88 or later and a C compiler (SQLite and aws-lc are built statically; on Windows, install the Visual Studio C++ Build Tools).

```bash
cp config.example.toml config.toml   # edit your profile, watchlist and AI provider
cargo run --release
```

On first launch an empty `portfolio.json` is created and the app starts in **Radar mode**. Press `?` in the app for all keyboard shortcuts.

Main commands (press `:`):

| Command | Effect |
|---|---|
| `cash 400` | set the cash to invest and show the allocation plan |
| `invest IWDA.AS 100` | log a purchase of €100 at the current price |
| `alert IWDA.AS 10` | get notified at +10% above your cost basis |

To tune the statement parser without recompiling: `invest-tui --test-parser imports/statement.pdf`.

## Privacy

`config.toml`, `portfolio.json`, `invest.db`, `imports/` and `logs/` are excluded by `.gitignore`. Never commit API keys: use `"env:VARIABLE_NAME"` in `config.toml` or the standard environment variables (`ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, …).

## Stack

Rust, Tokio, ratatui, crossterm, reqwest, tokio-tungstenite, rusqlite, serde.

## Disclaimer

invest-tui is a decision-support tool, not financial advice. Market data comes from free, unofficial endpoints that may change or be rate-limited. Always verify before placing an order.
