# Deel plugin for Cursor and Grok Bot

Connect Cursor and Grok Bot to [Deel](https://www.deel.com) through the official
[Deel MCP server](https://developer.deel.com/mcp/introduction).

Grok Bot installs plugins from the Cursor marketplace, so one plugin serves both.

## Installation

In Cursor or Grok Bot, locate **Plugins**, search for **Deel**, and install.

On first use of a Deel tool, the client opens the Deel authorization screen in the
browser. Sign in with your Deel account. Do not paste an API token into chat.


## What it can do

Contracts and EOR hiring, people and HRIS, time off, invoices and payments, payslips,
benefits and compliance, IT assets, onboarding and immigration, and reference data
(countries, currencies, job titles).

Write actions change data or money. Examples are creating or amending contracts,
time-off requests, invoice adjustments, and off-cycle payments. The included skill
instructs the agent to show the exact parameters and ask for your approval first. This
is guidance, not a technical control. Review every write call that your client asks you
to approve, and do not give an unattended agent write scopes it does not need.

## Authentication

The plugin connects only to `https://api.letsdeel.com/mcp`. The server uses OAuth2
with Dynamic Client Registration. The client registers itself, so you configure no API key and no client secret.

Network endpoints:

- `https://api.letsdeel.com/mcp` — hosted MCP server (HTTP, JSON-RPC 2.0, SSE)
- `https://api.letsdeel.com/.well-known/oauth-protected-resource/mcp` — OAuth discovery
- `https://api.letsdeel.com/.well-known/oauth-authorization-server` — OAuth discovery

## Permission levels

| Level | Scope | Examples |
| --- | --- | --- |
| Organization | Organization-wide data. Needs organization-level scopes. | `getListOfPeople`, `listItAssets` |
| Worker | Your own worker data. Needs worker-level authorization. | `createTimeOffRequest`, `getTimeOffRequests` |
| Public | Reference data. Needs no permissions. | `retrieveCountries`, `retrieveSupportedCurrencies` |

OAuth scopes:


Grant only the scopes you need. For example, grant `contracts:read` and not
`contracts:write`.

## Try it

- "List my Deel contracts"
- "What time-off policies are available?"
- "Show my organization details"

## Troubleshooting

- **401 Unauthorized:** restart the MCP connection to run OAuth again. Access tokens expire after 1 hour.
- **Tools do not appear:** the URL must be exactly `https://api.letsdeel.com/mcp`, with no trailing slash. The client must support HTTP transport.
- **No consent screen:** check that your browser does not block pop-ups or redirects.

## What the plugin contains

| File | Purpose |
|---|---|
| `.cursor-plugin/plugin.json` | Plugin metadata |
| `mcp.json` | MCP server configuration |
| `skills/deel/SKILL.md` | Guidance for safe use of the Deel tools |
| `assets/logo.svg` | Plugin logo |

The plugin has no hooks, scripts, or executable code.

## License

MIT. See [LICENSE](LICENSE). Use of the Deel MCP server is governed by the
[Deel Terms of Service](https://www.deel.com/legal/terms-of-service/). Deel handles personal data as described in the [Deel Privacy Policy](https://www.deel.com/legal/privacy-policy/).
