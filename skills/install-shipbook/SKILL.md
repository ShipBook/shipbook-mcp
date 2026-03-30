---
name: install-shipbook
description: Install and integrate the Shipbook SDK into a project. Detects the platform, installs the package, configures initialization, wraps existing loggers, sets correct log levels, integrates with Crashlytics if present, optionally adds API call logging, and runs a log audit at the end.
---

# Install Shipbook

A full integration assistant for adding the Shipbook SDK to a project.

## Steps

### 1. Detect or ask platform

Scan the project to detect the platform:
- `Podfile` or `.xcodeproj` → **iOS**
- `build.gradle` or `build.gradle.kts` → **Android**
- `pubspec.yaml` → **Flutter**
- `react-native` in package.json dependencies → **React Native**
- `package.json` with browser/web framework (react, vue, angular, svelte) → **Browser**
- `package.json` with node/express/nestjs/fastify → **Node.js**

If ambiguous, ask the user which platform.

### 2. Install SDK package

**iOS (Swift Package Manager):**
Add the package dependency `https://github.com/ShipBook/ShipBookSDK-iOS.git` via Xcode or Package.swift.

**Android (Gradle):**
Add to `build.gradle` dependencies:
```
implementation 'io.shipbook:shipbooksdk:1.+'
```

**Flutter:**
```
flutter pub add shipbook_flutter
```

**React Native:**
```
npm install @shipbook/react-native
```

**Browser:**
```
npm install @shipbook/browser
```

**Node.js:**
```
npm install @shipbook/node
```

### 3. Get or create app credentials

Call `get-account-apps` to list the user's apps (returns name, appId, platform, key).

- If matching apps exist, ask the user which one to use
- If no apps exist or the user wants a new one, call `create-app` with a name to create one
- Use the returned `appId` and `key` to auto-populate the initialization code

### 4. Initialize the SDK

Add the import at the top of the file (with the other imports) and the `start()` call at the correct initialization point per platform:

**iOS** — import at top, `start()` in `AppDelegate.application(_:didFinishLaunchingWithOptions:)`:
```swift
// top of file
import ShipBookSDK

// in didFinishLaunchingWithOptions
ShipBook.start(appId:"APP_ID", appKey:"APP_KEY")
```

**Android** — import at top, `start()` in `Application.onCreate()`:
```java
// top of file
import io.shipbook.shipbooksdk.ShipBook;

// in onCreate()
ShipBook.start(this, "APP_ID", "APP_KEY");
```

**Flutter** — import at top, `start()` in `main()`:
```dart
// top of file
import 'package:shipbook_flutter/shipbook_flutter.dart';

// in main()
Shipbook.start('APP_ID', 'APP_KEY');
```

**React Native** — import at top, `start()` at app initialization:
```javascript
// top of file
import shipbook from '@shipbook/react-native';

// at app initialization
shipbook.start("APP_ID", "APP_KEY");
```

**Browser** — import at top, `start()` early in app setup:
```javascript
// top of file
import Shipbook from '@shipbook/browser';

// early in app setup
await Shipbook.start("APP_ID", "APP_KEY");
```

**Node.js** — import at top, `start()` at startup before running server:
```javascript
// top of file
import { Shipbook } from '@shipbook/node';

// at startup
await Shipbook.start("APP_ID", "APP_KEY");
```

### 5. Detect and wrap existing loggers

Scan the project for existing logging libraries:

**Android — Timber:**
If `timber` is in dependencies, wrap it instead of replacing:
```kotlin
ShipBook.addWrapperClass(Timber::class.java.name)
Timber.plant(object : Timber.Tree() {
    override fun log(priority: Int, tag: String?, message: String, t: Throwable?) {
        Log.message(tag, message, priority, t)
    }
    init {
        ShipBook.addWrapperClass(this.javaClass.name)
    }
})
```

**iOS — SwiftLog:**
If `swift-log` is in dependencies, use the Shipbook handler:
```swift
import LoggingShipbook
LoggingSystem.bootstrap(ShipbookLogHandler.init)
```
Add `https://github.com/ShipBook/swift-log-shipbook.git` as a dependency.

**Any platform (except Flutter) — Custom logger wrapper class:**
If the project has a custom logger wrapper class, register it so Shipbook captures the correct file/line info:
```javascript
// Node.js / Browser / React Native
Shipbook.addWrapperClass(MyLogger);
```
```kotlin
// Android
ShipBook.addWrapperClass(MyLogger::class.java.name)
```
```swift
// iOS — create a wrapper function for each severity level (e, w, i, d, v),
// passing the caller's file/function/line so Shipbook shows the correct source location
func e(_ msg: String, tag: String? = nil, function: String = #function, file: String = #file, line: Int = #line) {
    log.e(msg: msg, tag: tag, function: function, file: file, line: line)
}
func w(_ msg: String, tag: String? = nil, function: String = #function, file: String = #file, line: Int = #line) {
    log.w(msg: msg, tag: tag, function: function, file: file, line: line)
}
func i(_ msg: String, tag: String? = nil, function: String = #function, file: String = #file, line: Int = #line) {
    log.i(msg: msg, tag: tag, function: function, file: file, line: line)
}
// ... same pattern for d() and v()
```

**If no wrapper found:**
Replace raw log calls with Shipbook logger calls. Create logger instances per module: `const log = Shipbook.getLogger("ModuleName")`

For calls that already have a level (e.g., `console.error`, `console.warn`, `Log.d(TAG, msg)`), map to the corresponding Shipbook level.

For calls without a level (`console.log`, `print()`, `NSLog()`), do NOT blindly map them all to one level. Look at the context of each log statement and assign the correct level:
- **Verbose** (`.v()`): Development-only — should never be compiled into production. Temporary dev logs → `.v()` or remove entirely.
- **Debug** (`.d()`): Lowest level acceptable in production but typically disabled. Server communication (excluding passwords), function entry/exit of important methods.
- **Info** (`.i()`): App progress and user navigation — app initialization, screen transitions, API calls (URL, status, response time only — not full payloads).
- **Warning** (`.w()`): Potentially harmful situations that aren't definitive errors. Use sparingly — e.g., repeated failed login attempts (3+ times), recurring network issues. Network connection failures (no internet, timeout) are not errors — a single failed attempt should be Debug at most; only escalate to Warning after repeated failures (3+ attempts).
- **Error** (`.e()`): Recoverable error events where the app continues running — null pointer exceptions, server response parsing failures, server-returned errors. Not for network connectivity failures.
- **WTF/Assert**: Fatal errors leading to application exit — error level with full stack trace.

### 6. Detect and integrate Crashlytics

Scan for Firebase Crashlytics in dependencies (`firebase-crashlytics`, `FirebaseCrashlytics`).

If found, modify the `start()` call to pass the session URL to Crashlytics:

**Swift:**
```swift
ShipBook.start("APP_ID", appKey:"APP_KEY") { (sessionUrl: String) -> () in
    Crashlytics.crashlytics().setCustomValue(sessionUrl, forKey: "ShipbookSession")
}
```

**Kotlin:**
```kotlin
ShipBook.start(this, "APP_ID", "APP_KEY") { sessionUrl ->
    FirebaseCrashlytics.getInstance().setCustomKey("shipbookSession", sessionUrl)
}
```

**Java:**
```java
ShipBook.start(this, "APP_ID", "APP_KEY", (sessionUrl) -> {
    FirebaseCrashlytics.getInstance().setCustomKey("shipbookSession", sessionUrl);
    return Unit.INSTANCE;
});
```

### 7. Ask about API call logging

Ask the user: "Should I add logging to all API calls?"

If yes, detect the setup and add logging:

**If framework middleware is available:**
- **Express (Node.js):** `app.use(Shipbook.expressMiddleware())`
- **NestJS:** `app.useGlobalInterceptors(Shipbook.nestjsInterceptor())`

**If API calls are centralized** (single API client/service class, e.g., an `apiClient.ts`, `NetworkManager`, `ApiService`):
Add logging directly in that central place — log URL, method, status code, and duration at Info level.

**If neither** (scattered fetch/axios/HTTP calls):
- **Browser/React Native:** Wrap `fetch` or `axios` with a logging interceptor
- **Android:** Add an OkHttp interceptor
- **iOS:** Add a URLSession wrapper

Log format: `log.i("API {METHOD} {URL} → {STATUS} ({DURATION}ms")`

### 8. Register user (if app has auth)

If the project has authentication (login flow, auth context, user session), add `registerUser()` after successful login and `logout()` on logout:

**iOS (Swift):**
```swift
ShipBook.registerUser(userId: "USER_ID", userName: "USER_NAME", fullName: "FULL_NAME", email: "EMAIL", phoneNumber: "PHONE", additionalInfo: ["key": "value"])
// on logout:
ShipBook.logout()
```

**Android (Kotlin):**
```kotlin
ShipBook.registerUser(userId = "USER_ID", userName = "USER_NAME", fullName = "FULL_NAME", email = "EMAIL", phoneNumber = "PHONE", additionalInfo = mapOf("key" to "value"))
// on logout:
ShipBook.logout()
```

**Flutter (Dart):**
```dart
Shipbook.registerUser(userId: 'USER_ID', userName: 'USER_NAME', fullName: 'FULL_NAME', email: 'EMAIL', phoneNumber: 'PHONE', additionalInfo: {'key': 'value'});
// on logout:
Shipbook.logout();
```

**React Native / Browser (JavaScript):**
```javascript
Shipbook.registerUser("USER_ID", "USER_NAME", "FULL_NAME", "EMAIL", "PHONE", { key: "value" });
// on logout:
Shipbook.logout();
```

**Node.js:** User info is per-request via middleware context — `registerUser()` is not needed.

### 9. Run audit

After completing the integration, run the `audit-logs` skill to scan for PII in logs, verify log level correctness, and check for sensitive data exposure.

## Output

- Confirm all changes made with file paths
- Show the initialization code with the actual appId and key
- List any wrappers detected and integrated
- Report Crashlytics integration status
- Report API logging additions
- Present the audit results from the audit-logs skill
