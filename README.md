# Shipbook MCP

Connect [Shipbook](https://shipbook.io) to any AI coding assistant to investigate issues using real application logs. Shipbook's Loglytics engine automatically classifies errors and provides structured insights, while powerful log search lets AI agents explore runtime events. Logs include rich context — severity, user, device, session, and the exact file and code location — giving your assistant the ground truth it needs to debug effectively.

**Prerequisites:** A [Shipbook](https://shipbook.io) account. [Get started here](https://www.shipbook.io/docs).

## Supported Platforms

Works with any MCP-compatible AI assistant:

- [Cursor](#cursor)
- [Claude Code](#claude-code)
- [Claude.ai](#claudeai)
- [VS Code (Copilot)](#vs-code-copilot)
- [Codex](#codex)
- [Google Antigravity](#google-antigravity)
- [Windsurf](#windsurf)
- [Generic MCP Client](#generic-mcp-client)

## MCP Tools

| Tool | Description |
|------|-------------|
| **get-account-apps** | List all apps in your account (name, appId, platform, key) |
| **create-app** | Create a new app and get its credentials (name, appId, key) |
| **get-loglytics-errors** | Retrieve grouped/classified errors with occurrence counts |
| **get-logs** | Search logs with 20+ filters (severity, user, device, time, etc.) |

## AI Skills

Pre-built investigation and integration workflows (available in Cursor and Claude Code):

| Skill | Description |
|-------|-------------|
| **install-shipbook** | Full SDK integration assistant — installs package, detects wrappers, sets log levels, integrates Crashlytics, adds API logging, audits for PII |
| **audit-logs** | Scan codebase for PII in logs, incorrect log levels, and sensitive data leaks |
| **list-issues** | List and analyze classified errors/warnings from Loglytics, group by root cause |
| **debug-session** | Trace a user's session to understand what happened |
| **search-logs** | Search logs using natural language queries |

## Installation

### Cursor

**From Cursor Marketplace:**
Search for "Shipbook" in the Cursor marketplace and click Install.

**One-Click Install:**

<a href="cursor://anysphere.cursor-deeplink/mcp/install?name=shipbook&config=eyJ1cmwiOiJodHRwczovL2FwaS5zaGlwYm9vay5pby9tY3AiLCJhdXRoIjp7IkNMSUVOVF9JRCI6ImExZGI4ZGY1LWNlYjUtNDAxMy04YzM1LWFmOWU0NTdjNjliNSJ9fQ">
  <img src="https://cursor.com/deeplink/mcp-install-dark.svg" alt="Install Shipbook MCP in Cursor" height="32" />
</a>

**Manual Configuration:**
Open Cursor Settings → Tools & MCP → Add MCP server:
```json
{
  "mcpServers": {
    "shipbook": {
      "url": "https://api.shipbook.io/mcp",
      "auth": {
        "CLIENT_ID": "a1db8df5-ceb5-4013-8c35-af9e457c69b5"
      }
    }
  }
}
```

### Claude Code

```terminal
claude mcp add --transport http shipbook-mcp https://api.shipbook.io/mcp
```

On first use, Claude Code opens your browser to log in and authorize access.

### Claude.ai

1. Go to [claude.ai](https://claude.ai) → **Settings** → **Connectors**
2. Click **Add Connector** and enter: `https://api.shipbook.io/mcp`
3. Click **Connect** — you'll be redirected to Shipbook to authorize access

### VS Code (Copilot)

**One-Click Install:**

<a href="vscode:mcp/install?%7B%22name%22%3A%22shipbook%22%2C%22config%22%3A%7B%22type%22%3A%22http%22%2C%22url%22%3A%22https%3A//api.shipbook.io/mcp%22%7D%7D">
  <img src="https://img.shields.io/badge/Install_MCP_Server-VS_Code-0098FF?style=flat-square&logo=visualstudiocode&logoColor=white" alt="Install Shipbook MCP in VS Code" height="32" />
</a>

**Manual:** Open Command Palette → MCP: Add Server → HTTP → `https://api.shipbook.io/mcp`

### Codex

Add to `~/.codex/config.toml`:
```toml
[mcp_servers.shipbook]
url = "https://api.shipbook.io/mcp"
```

Then: `codex mcp login shipbook`

### Google Antigravity

Open MCP store → Manage MCP Servers → View raw config → Add:
```json
{
  "mcpServers": {
    "shipbook": {
      "url": "https://api.shipbook.io/mcp"
    }
  }
}
```

### Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`:
```json
{
  "mcpServers": {
    "shipbook": {
      "url": "https://api.shipbook.io/mcp"
    }
  }
}
```

### Generic MCP Client

MCP endpoint: `https://api.shipbook.io/mcp`

Standard `mcpServers` configuration:
```json
{
  "mcpServers": {
    "shipbook": {
      "url": "https://api.shipbook.io/mcp"
    }
  }
}
```

## Authentication

Shipbook MCP uses **OAuth 2.1** for secure authentication. On first use, you'll be redirected to Shipbook to log in and authorize access. Your credentials are never shared with the AI assistant.

For MCP clients that don't support OAuth, you can use an API key from the [Authentication Keys](https://www.shipbook.io/docs/account_preferences/authentication-keys) section in the Shipbook console:
```json
{
  "mcpServers": {
    "shipbook": {
      "url": "https://api.shipbook.io/mcp",
      "headers": {
        "Authorization": "Bearer YOUR_AUTH_TOKEN"
      }
    }
  }
}
```

## Usage Examples

- "Install Shipbook in my project"
- "Audit my logs for PII and incorrect log levels"
- "Find errors in my app and suggest fixes"
- "What happened to the user with email test@example.com?"
- "Show me logs from the last hour with severity Error"
- "Investigate the most common crash in my iOS app"
- "Debug the session where user X reported a problem"
- "Fix the issues found in Shipbook Loglytics"

## Documentation

- [Shipbook MCP Documentation](https://www.shipbook.io/docs/mcp)
- [Shipbook Website](https://shipbook.io)

## License

MIT
