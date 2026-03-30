---
name: debug-session
description: Debug a specific user session in Shipbook. Finds logs for a user by email, device ID, or user ID, then traces the full session to understand what happened.
---

# Debug User Session

Use Shipbook MCP tools to debug what happened in a specific user's session.

## Steps

1. Call `get-account-apps` to identify the app (returns name, appId, platform, key). If the user didn't specify an app, detect the current project's platform and filter matching apps. If there are still multiple matches, ask the user which one.
2. Call `get-logs` filtered by the user identifier provided (email, userId, userName, udid) with severity=Error to find problems.
3. If errors are found, take the `session` ID from the error log and call `get-logs` again with `maxOrderId` set to the error's orderId + 10 and without severity filter — this shows the context leading up to the error.
4. Every log includes `fileName` and `lineNumber` — use these to locate and read the actual code in the project to understand what happened.
5. If no errors are found, broaden the search (remove severity filter, adjust time range) to find the user's activity.
6. Use `minOrderId`/`maxOrderId` to navigate through long sessions.

## Output

- Reconstruct the user's session timeline chronologically
- Highlight any errors or warnings encountered, with their fileName and lineNumber
- Identify the sequence of events leading to any issues
- Suggest fixes based on both the log context and the actual code at the error location
