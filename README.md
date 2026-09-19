# shopingcart1

## Webull MCP: historical MNQ / MES futures data

This project is wired up to Webull's official MCP server (`webull-openapi-mcp`),
so Claude can pull historical futures bars for MNQ (Micro E-mini Nasdaq-100)
and MES (Micro E-mini S&P 500) directly. The server is registered in
[`.mcp.json`](./.mcp.json) and launched automatically by Claude Code in this
project.

### One-time setup

1. **Install [`uv`](https://docs.astral.sh/uv/)** (provides `uvx`), if you
   don't already have it:
   ```bash
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```

2. **Create a Webull Developer app** at https://developer.webull.com and
   generate an App Key / App Secret.

3. **Activate a market data subscription** for futures at
   https://www.webullapp.com/quote (US region) — without this the futures
   endpoints return no data even with valid credentials.

4. **Configure credentials.** Copy the template and fill in your key/secret:
   ```bash
   cp .env.example .env
   ```

5. **Authenticate the MCP server** (runs its own `.env`-based init/auth flow,
   approve the login request in the Webull mobile app if your account uses
   2FA):
   ```bash
   uvx webull-openapi-mcp init
   uvx webull-openapi-mcp auth
   ```

6. Restart Claude Code (or start a new session) in this project so it picks
   up the `webull` MCP server from `.mcp.json`.

### Fetching historical MNQ / MES data

Once connected, ask Claude directly, e.g.:

- "Get the last 30 days of 5-minute MNQ futures bars"
- "Pull daily MES OHLCV bars for the past year"

Under the hood this calls the `get_futures_bars` tool, which takes a
`symbol` (`MNQ`, `MES`), a `timeframe`/`interval` (e.g. `1min`, `5min`,
`1day`), and a start/end date or bar count. Other futures tools exposed by
the server: `get_futures_tick`, `get_futures_snapshot`, `get_futures_depth`,
`get_futures_footprint`.

### Notes

- `.env` is git-ignored — never commit real credentials.
- `WEBULL_ENVIRONMENT` defaults to `prod`; the server otherwise defaults to
  its sandbox/UAT environment, which won't return real market data.
- Futures market data is currently only supported for the US and HK regions.
