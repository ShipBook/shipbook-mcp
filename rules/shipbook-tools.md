---
description: Guidelines for using Shipbook MCP tools to search logs, investigate errors, and debug user sessions
alwaysApply: true
---

# Shipbook Tools Usage Guide

You have access to Shipbook MCP tools for cloud log analysis.

## Available Tools

1. **get-account-apps** — Lists all apps in the account (name, appId, platform, key, integration). Call this first to get the appId and key needed by other tools and for SDK initialization.

2. **create-app** — Creates a new app in the account. Returns name, appId, and key. Platform is detected automatically on first SDK session.
   - Required: `name`

3. **get-loglytics-errors** — Returns grouped/classified recurring errors for an app. Shows occurrence count, affected devices, sessions, app versions, and OS versions.
   - Required: `appId`
   - Optional: `severity` (Error, Warning), `appVersion`

4. **get-logs** — Searches and retrieves individual log entries.
   - Required: `appId`
   - Severity: `severity` — Error, Warning, Info, Debug, Verbose
   - Text search: `msg` (free text in messages)
   - Code location: `tag`, `fileName`, `lineNumber`
   - Device: `udid` (unique device ID), `deviceModel`, `manufacturer`
   - User: `userId`, `userName`, `fullName`, `email`, `phoneNumber`
   - App: `appVersion`
   - Loglytics: `key` (error key from get-loglytics-errors — finds all logs for a specific classified error)
   - Session: `session` (session ID — retrieves all logs from that session)
   - Pagination: `minOrderId` (logs after this order), `maxOrderId` (logs before this order)
   - Time range: `fromTime`, `toTime` (ISO format)
   - Limit: `limit` (default: 50)

## Investigation Workflow

1. **Start with get-account-apps** to identify the target app and get its `appId`
2. **Use get-loglytics-errors** to see grouped errors — identifies the most frequent and impactful issues
3. **Use get-logs with severity=Error** and relevant filters (udid, userId, email) to find specific error instances
4. **Get context around a specific log**: call get-logs again with the same `session` ID, set `maxOrderId` to the log's orderId + 10, and remove the severity filter. This shows what happened leading up to that log.
5. **Every log has `fileName` and `lineNumber`** — always use these to locate and read the actual code in the project. This is the best way to understand what the log means and find the root cause.
6. **Use `key` from loglytics errors** in get-logs to find all instances of a specific classified error
7. **Use `minOrderId`/`maxOrderId`** to paginate within a session (get logs before/after a specific log entry)

## Time Filters

When a user mentions a specific time, set `fromTime` to 10 minutes before and `toTime` to 10 minutes after that time.

## Response Format

Present log findings clearly. Do not dump raw JSON. Highlight:
- Error messages and stack traces
- The sequence of events leading to the error
- Affected user/device information
- Suggestions for code fixes when the error cause is apparent
