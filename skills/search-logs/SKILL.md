---
name: search-logs
description: Search Shipbook logs with filters. Find logs by message text, severity, user, device, time range, file name, or tag.
---

# Search Logs

Use Shipbook MCP tools to search for specific log entries.

## Steps

1. Call `get-account-apps` to identify the target app if not already known (returns name, appId, platform, key). If the user didn't specify an app, detect the current project's platform and filter matching apps. If there are still multiple matches, ask the user which one.
2. Translate the user's natural language query into appropriate get-logs filters:
   - Severity → `severity` (Error, Warning, Info, Debug, Verbose). When investigating issues, start with Error.
   - Text search → `msg` (free text search in log messages)
   - Code location → `fileName`, `tag`, `lineNumber`
   - Device → `udid` (unique device ID — use for specific device searches), `deviceModel`, `manufacturer`
   - User → `userId`, `userName`, `fullName`, `email`, `phoneNumber`
   - App → `appVersion`
   - Loglytics → `key` (error key from get-loglytics-errors — finds all logs for a specific classified error)
   - Session → `session` (session ID — retrieves all logs from that session. After finding an error, use its session ID without severity filter to see full context)
   - Pagination → `minOrderId` (logs after this order), `maxOrderId` (logs before this order) — use to navigate within a session
   - Time range → `fromTime`, `toTime` (ISO format. If user mentions a specific time, set 10-minute window around it)
   - Limit → `limit` (default: 50)
3. When analyzing a specific log entry, get its surrounding context: call `get-logs` again with the same `session` ID, set `maxOrderId` to the log's orderId + 10, and remove severity filter. This shows what happened leading up to that log.
4. Every log includes `fileName` and `lineNumber` — use these to locate and read the actual code in the project. This helps understand what the log means in context.
5. Present results as a readable summary, not raw JSON.
6. If too many results, suggest narrowing filters. If no results, suggest broadening.

## Output

- Summarize matching logs with timestamps, severity, and message
- Group related logs if patterns emerge
- For each log of interest, show the fileName and lineNumber so the user can navigate to it
- Offer to dive deeper into any specific log or session (get context via session + maxOrderId)
