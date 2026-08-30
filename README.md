# Angel One Connector with Claude AI

**Trading MCP** — live prices, portfolio, holdings and charts from your Angel One account, in
plain English.

Published by **TryTech**. Not affiliated with or endorsed by Angel One or Anthropic.

---

## Install

Double-click `angel-one-connector.mcpb`, or drag it into the Claude Desktop window. Claude
Desktop shows a settings form. Fill in four values and you're done — no config file, no
terminal, no Node.js install.

| Field | Where it comes from |
|---|---|
| **API Key** | smartapi.angelone.in → My Smart API & Apps → eye icon beside your app |
| **Client Code** | Your Angel One profile, e.g. `A123456` |
| **MPIN** | Your Angel One login PIN — **not** your old password |
| **TOTP Secret** | smartapi.angelone.in/enable-totp → "Can't Scan? Copy the key" |

The MPIN and TOTP secret are marked sensitive: they are masked on input and are not written to
`claude_desktop_config.json` in plain text.

## Before you install

You still need to do three things on Angel One's side. No extension can do these for you:

1. **Create a SmartAPI app.** Redirect URL must be a public URL — `https://www.google.com`
   works. Localhost is rejected.
2. **Allowlist your public IP** in the app's mandatory *Primary Static IP* field.
   **Angel One only lets this change once per week**, and most home broadband rotates its IP on
   router restart. If yours changes, API calls fail until you can update it.
3. **Enable TOTP** and save the base32 secret. It cannot be recovered later.

## Order safety — read this

**There is no dry-run mode in this server.** The `place_order` tool reaches the real exchange.

Two things protect you:

- **Claude declines to place trades on your behalf.** It fetches prices, sizes positions and
  shows what an order would look like — you place it yourself in the Angel One app.
- **Enforced order limits.** This extension ships with a hard cap of **1 share** and
  **₹1,000** per order. Hard limits cannot be overridden by any argument. Raise them in
  settings only if you deliberately intend to place larger orders.

If you want credentials that *physically cannot* trade, create a **Market Feeds API** key
instead of a Trading API key. You lose holdings, positions and funds; you gain a setup with no
order capability at all.

## What it does

Live prices and quotes · historical candles (1-minute to daily) · holdings, positions, funds,
order book and trade book · position sizing · rule-based screening · gainers and losers ·
put-call ratio · open interest · option Greeks · charge and margin estimates.

## What it does not do

- **Run continuously.** It acts when you send a message. No background monitoring or alerts.
- **Work outside market hours.** Live data is 09:15–15:30 IST on trading days. Historical data
  works any time.
- **Give trading advice.** It reads the data you request. No backtest, no strategy, no risk model.

## Platforms

macOS and Windows. Claude Desktop extensions are not available on Linux — Linux users should
use the manual `claude_desktop_config.json` setup from the starter kit instead.

## Credits

This extension bundles [`angel-one-mcp`](https://github.com/ameernoufil/angel-one-mcp) v1.0.10
by Mohamed Ameer Noufil N, used under the MIT License. The upstream license is included at
`server/node_modules/angel-one-mcp/LICENSE`.

The server version is pinned and bundled deliberately: it receives your MPIN and TOTP secret on
every run, so it should not silently update underneath you.

---

*This describes a technical integration using Angel One's official SmartAPI. It is not
investment advice and describes no trading strategy. Trading involves risk of loss.*
