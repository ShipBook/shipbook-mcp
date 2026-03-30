---
name: audit-logs
description: Audit log statements in the codebase for PII exposure and sensitive data leaks. Scans all logging calls and reports issues. Does not make changes — only reports findings and asks the user before fixing anything.
---

# Audit Logs

Scan the codebase for logging issues — PII exposure, secrets, and sensitive data leaks.

**Important:** This skill is read-only. It reports findings but does NOT make changes automatically. Always present the report first and ask the user what they want to fix.

## Steps

### 1. Find all log statements

Scan the project for all logging calls. Look for:
- Shipbook: `log.e()`, `log.w()`, `log.i()`, `log.d()`, `log.v()`, `Log.e()`, `Log.w()`, etc.
- Standard: `console.log`, `console.error`, `console.warn`, `print()`, `NSLog()`, `android.util.Log`
- Framework loggers: `logger.info()`, `logger.error()`, `Timber.d()`, etc.

### 2. Check for secrets and sensitive data (high priority)

These must never appear in logs. Flag immediately:

- **Passwords and secrets** — variable names like `password`, `secret`, `token`, `apiKey`, `accessToken`, `refreshToken`, `authorization`
- **Full credit card numbers** — patterns like `\d{4}[-\s]?\d{4}[-\s]?\d{4}[-\s]?\d{4}`. Logging the last 4 digits is acceptable (e.g., `**** **** **** 1234`), but the full number must never be logged.
- **SSN, national ID, and passport numbers** — variable names like `ssn`, `socialSecurity`, `nationalId`, `idNumber`, `passportNumber`, `passport`. These are government-issued identifiers used for identity theft and are protected under GDPR, CCPA, and most privacy laws. They must never appear in logs.

These are non-negotiable — present them as critical findings that need immediate attention.

### 3. Check for personal identifiers (ask user)

These are suggestions — the user may have valid reasons to log them. Present as recommendations and ask before changing:

- Email addresses logged directly — suggest using userId or hashed identifier instead
- Phone numbers — variable names like `phone`, `phoneNumber`, `mobile`
- Physical addresses — `address`, `street`, `zipCode`, `postalCode`
- Full names — `fullName`, `firstName`, `lastName` logged directly in message strings

**Recommendation example:**
```
// Consider replacing direct PII with internal identifiers:
log.i(`User logged in: ${user.email}`)
// Could be:
log.i(`User logged in: ${user.id}`)
```

Ask the user: "I found personal identifiers in these log statements. Would you like me to replace them with internal IDs, or do you prefer to keep them as-is?"

### 4. Check log levels (suggestions only)

These are observations, not errors. The developer may have intentional reasons for their log level choices. Present as suggestions and let the user decide.

- **Verbose**: Development-only — should never be compiled into production. Flag any `.v()` calls in production code paths.
- **Debug**: Lowest level acceptable in production but typically disabled. Used for server communication (excluding passwords) and function entry/exit of important methods.
- **Info**: App progress and user navigation — app initialization, screen transitions, API calls (URL, status, response time only — not full payloads).
- **Warning**: Potentially harmful situations that aren't definitive errors. Use sparingly — e.g., repeated failed login attempts (3+ times), recurring network issues. Note: network connection failures (no internet, timeout, unreachable host) are NOT errors — sometimes there is simply no internet. A single failed connection attempt should be Debug at most. Only escalate to Warning after repeated failures (3+ attempts).
- **Error**: Recoverable error events where the app continues running — null pointer exceptions, server response parsing failures, server-returned errors. Do NOT flag network connectivity failures as errors.

Do NOT assume existing log levels are wrong. Present observations as "you might want to review these" and ask the user if they'd like any changes.

### 5. Check sensitive Android views

If the project is Android and uses Shipbook:
- Scan layouts for `EditText` with `inputType="textPassword"`, `inputType="number"` (credit cards), or similar sensitive input fields
- Check if `ShipBook.ignoreViews()` is called for these views
- If not, suggest adding `ShipBook.ignoreViews(R.id.passwordField, R.id.creditCardField)`

### 6. Report findings

Present results grouped by priority:

**Critical** — Secrets/passwords/full credit card numbers in logs (must fix)
**Suggestions** — Personal identifiers and log level observations (ask user)

For each finding, show:
- File path and line number
- The log statement in question
- What was found
- Suggested alternative

After presenting the report, ask the user which findings they'd like to fix. Do not make changes without explicit approval.

## Output

- Total log statements scanned
- Critical findings (secrets/passwords/CC numbers) — these need fixing
- Suggestions (personal identifiers, log levels) — ask user preference
- For each finding: file, line, what was found, suggested fix
- Ask: "Which of these would you like me to fix?"
