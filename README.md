# Shipbook Plugin for Cursor

Connect [Shipbook](https://shipbook.io) to [Cursor](https://cursor.com) to investigate issues using real application logs. Shipbook's Loglytics engine automatically classifies errors and provides structured insights, while powerful log search lets AI agents explore runtime events. Logs include rich context such as severity, user, device, session, and the exact file and code location where issues occur, allowing Cursor to trace failures and suggest fixes based on the ground truth of your system.

**Prerequisites:** A [Shipbook](https://shipbook.io) account with at least one app. [Get started here](https://docs.shipbook.io).

## Features

### MCP Tools

| Tool | Description |
|------|-------------|
| **get-account-apps** | List all apps in your Shipbook account |
| **get-loglytics-errors** | Retrieve grouped/classified errors with occurrence counts |
| **get-logs** | Search logs with 20+ filters (severity, user, device, time, etc.) |

### AI Skills

Pre-built investigation workflows:

| Skill | Description |
|-------|-------------|
| **investigate-errors** | Diagnose errors using Loglytics data and session context |
| **debug-session** | Trace a user's session to understand what happened |
| **search-logs** | Search logs using natural language queries |

### Rules

Built-in AI guidance for optimal tool usage, including investigation workflows and best practices.

## Installation

### From Cursor Marketplace

Search for "Shipbook" in the Cursor marketplace and click Install.

### One-Click Install

<a href="cursor://anysphere.cursor-deeplink/mcp/install?name=shipbook&config=eyJ1cmwiOiJodHRwczovL2FwaS5zaGlwYm9vay5pby9tY3AiLCJhdXRoIjp7IkNMSUVOVF9JRCI6ImExZGI4ZGY1LWNlYjUtNDAxMy04YzM1LWFmOWU0NTdjNjliNSJ9fQ">
  <img src="https://cursor.com/deeplink/mcp-install-dark.svg" alt="Install Shipbook MCP in Cursor" height="32" />
</a>

## Authentication

The plugin uses **OAuth 2.1** for secure authentication. On first use, you will be redirected to Shipbook to log in and authorize access. Your credentials are never shared with Cursor.

## Usage Examples

- "Find errors in my app and suggest fixes"
- "What happened to the user with email test@example.com?"
- "Show me logs from the last hour with severity Error"
- "Investigate the most common crash in my iOS app"
- "Debug the session where user X reported a problem"
- "Fix the issues found in Shipbook Loglytics"

## Documentation

- [Shipbook MCP Documentation](https://docs.shipbook.io/mcp)
- [Shipbook Website](https://shipbook.io)

## License

MIT
