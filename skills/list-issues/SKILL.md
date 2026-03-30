---
name: list-issues
description: List and analyze classified errors and warnings from Shipbook Loglytics. Retrieves the full list of issues, analyzes the code at each error location, groups them by root cause, and suggests fixes.
---

# List Issues

Use Shipbook MCP tools to list and analyze classified errors and warnings in an application. Defaults to errors — set severity to Warning for warnings.

## Steps

1. Call `get-account-apps` to list available apps (returns name, appId, platform, key). If the user didn't specify an app, detect the current project's platform from the codebase and filter matching apps. If there are still multiple matches, ask the user which one.
2. Call `get-loglytics-errors` with the appId to retrieve grouped error categories.
3. Analyze the errors and group them by root cause — multiple error entries may stem from the same underlying issue (e.g., a network failure causing errors in different parts of the code, or one null value propagating through several functions). Present grouped by reason, not just as a flat list.
4. Present error groups ranked by impact (total occurrence count, affected devices/sessions across all errors in the group).
5. For the most critical error group (or the one the user asks about), use the `key` of each error in the group with `get-logs` to find specific instances.
6. Error logs include `fileName` and `lineNumber` — use these to locate the exact code in the project and analyze it. Reading the actual code is often the best way to understand why the error happens.
7. To get context around a specific error, call `get-logs` again with the same `session` ID, set `maxOrderId` to the error's orderId + 10, and remove the severity filter. This shows what happened leading up to the error.
8. Summarize findings: what the root cause is, how often it occurs, which users/devices are affected, and suggest code fixes based on the code analysis and log context.

## Output

- First present each error individually ranked by impact (occurrence count, affected devices/sessions)
- Then group them by root cause — show which errors stem from the same underlying issue and explain why
- For investigated errors, show the chain of events leading to the crash/error
- Suggest concrete fixes when the root cause is visible in the logs
- If the user's codebase is open, offer to locate and fix the relevant code
