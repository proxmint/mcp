# Proxmint MCP server

Connect Claude, Cursor or any MCP client to your [Proxmint](https://proxmint.com) account. Read a web page through
your own residential, datacenter or mobile proxies, from the country you choose. Ask what a pack costs, check your
balance and the bandwidth left, see daily usage, generate proxy lines for a country or city with sticky sessions,
and test a connection.

The server is hosted, so there is nothing to install: `https://proxmint.com/api/mcp` (Streamable HTTP). It is listed
in the official MCP Registry as `com.proxmint/mcp`.

## Tools

| Tool | What it returns | Needs a key |
| --- | --- | --- |
| `get_pricing` | Pack sizes, prices and price per GB for residential, premium residential, datacenter and mobile | No |
| `get_account` | Balance, and each product's status, bandwidth used and left, host, ports and username | Yes |
| `get_usage` | Gigabytes and requests per product per day, up to 90 days, with totals | Yes |
| `generate_proxies` | Proxy lines: HTTP or SOCKS5, country, state, city, ZIP or ASN targeting, rotating or sticky for 1 to 120 minutes | Yes |
| `check_connection` | One request through a product: exit IP, the country Cloudflare sees, time taken | Yes |
| `fetch_url` | A web page fetched through a product, from its IPs and targeting: status, final URL, title, and the page as readable text or raw HTML (up to 50,000 characters) | Yes |

Every tool only reads. The server cannot buy, top up, change targeting or rotate a password. Two tools send
traffic, both through your own product and on its bandwidth: `check_connection` sends one request of a few
kilobytes, and `fetch_url` loads the page you ask for. `fetch_url` follows up to 5 redirects, reads at most 1 MB,
leaves binary files unread, does not run JavaScript, and refuses private addresses and ports the gateway does not
forward.

## API key

Create a key under **Settings** in the [dashboard](https://proxmint.com/dashboard/settings). Keys start with
`pmk_` and are read-only. `generate_proxies` returns your proxy password, because a working line needs it, so
keep the key out of shared configs and revoke it in Settings if it leaks.

## Connect

Claude Code:

```bash
claude mcp add --transport http proxmint https://proxmint.com/api/mcp \
  --header "Authorization: Bearer pmk_YOUR_KEY"
```

Cursor (`~/.cursor/mcp.json`):

```json
{
  "mcpServers": {
    "proxmint": {
      "url": "https://proxmint.com/api/mcp",
      "headers": { "Authorization": "Bearer pmk_YOUR_KEY" }
    }
  }
}
```

Claude Desktop (`claude_desktop_config.json`), through `mcp-remote`:

```json
{
  "mcpServers": {
    "proxmint": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://proxmint.com/api/mcp", "--header", "Authorization:${AUTH}"],
      "env": { "AUTH": "Bearer pmk_YOUR_KEY" }
    }
  }
}
```

## Limits and privacy

Account tools share the customer API's limit of 60 requests a minute per IP. The server keeps nothing and does not
log tool arguments, results or keys. Full docs: [proxmint.com/docs/mcp](https://proxmint.com/docs/mcp).

## Links

- [Proxmint](https://proxmint.com)
- [Pricing](https://proxmint.com/pricing)
- [Customer API](https://proxmint.com/docs/api)
- [Targeting and sessions](https://proxmint.com/docs/targeting)

## License

MIT
