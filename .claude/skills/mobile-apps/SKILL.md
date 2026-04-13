---
name: mobile-apps
description: Mobile application development skill - triggered when building mobile apps for Android, iOS, or both. Guides framework selection (React Native, Flutter, Swift, Kotlin, .NET MAUI), native features, app store submission, push notifications, and offline support.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Agent
---

# Mobile Application Development Skill

## When This Skill Activates
- User mentions: mobile app, iOS app, Android app, React Native, Flutter, Swift, Kotlin
- During `/implement` when building for mobile
- When discussing app store, push notifications, offline mode

## Framework Selection Guide

### Ask First
1. Target platforms? (iOS only, Android only, both)
2. Team expertise? (JavaScript, Dart, Swift, Kotlin, C#)
3. App type? (Content, utility, social, e-commerce, enterprise, game)
4. Performance critical? (Animations, video, AR -> native or Flutter)
5. Existing web codebase to share? (Yes -> React Native or PWA)
6. Budget/timeline? (Tight -> cross-platform, Flexible -> native)

### Framework Decision Tree
```
Both platforms + JS team?         -> React Native (Expo recommended)
Both platforms + best UI?         -> Flutter (Dart)
Both platforms + C# team?        -> .NET MAUI
iOS only + best performance?     -> Swift + SwiftUI
Android only + best performance? -> Kotlin + Jetpack Compose
Simple app + existing website?   -> PWA (Progressive Web App)
Game or heavy graphics?          -> Unity or Unreal Engine
```

### Framework Comparison
| Framework | Language | Platforms | Performance | Native Feel | Hot Reload |
|-----------|---------|-----------|-------------|-------------|------------|
| **React Native (Expo)** | JS/TS | iOS + Android | Good | Good (native components) | Yes |
| **Flutter** | Dart | iOS + Android + Web + Desktop | Excellent | Custom (Material/Cupertino) | Yes |
| **SwiftUI** | Swift | iOS/iPadOS/macOS/watchOS | Excellent | Native Apple | Yes (Xcode) |
| **Jetpack Compose** | Kotlin | Android | Excellent | Native Android | Yes |
| **.NET MAUI** | C# | iOS + Android + Win + Mac | Good | Native | Yes |
| **Kotlin Multiplatform** | Kotlin | iOS + Android | Good | Native | Partial |

### Mobile Architecture
```
UI Layer (SwiftUI / Compose / React Native / Flutter)
  |
  |-- Navigation (stack, tabs, drawer)
  |-- State Management (Redux/Zustand, Riverpod/Bloc, SwiftUI @State)
  |
Service Layer
  |-- API Client (REST/GraphQL + auth tokens)
  |-- Local Storage (SQLite, Realm, AsyncStorage, Hive)
  |-- Push Notifications (APNs / FCM)
  |-- Background Tasks (sync, upload, location)
  |
Platform Layer
  |-- Camera, GPS, Biometrics, Bluetooth, NFC
  |-- Deep Links, Share Extensions, Widgets
```

### Mobile-Specific Patterns

**Offline-First Architecture**:
1. Local DB as source of truth (SQLite, Realm, Hive)
2. Sync queue for pending changes
3. Conflict resolution (last-write-wins or merge)
4. Background sync when connectivity restored

**Push Notifications**:
- iOS: APNs (Apple Push Notification service)
- Android: FCM (Firebase Cloud Messaging)
- Backend: Send via Firebase Admin SDK or AWS SNS
- Handle: foreground, background, and killed states

**Deep Linking**:
- Universal Links (iOS) / App Links (Android)
- Custom URL schemes (myapp://screen/123)
- Deferred deep links (link before install -> open after install)

### App Store Submission
| | Apple App Store | Google Play Store |
|---|----------------|------------------|
| **Account** | $99/year | $25 one-time |
| **Review** | 1-7 days (strict) | Hours to 3 days |
| **Guidelines** | Apple Human Interface | Material Design |
| **Privacy** | App Privacy Labels required | Data Safety section |
| **Payments** | 15-30% commission (IAP required) | 15-30% commission |
| **TestFlight/Beta** | TestFlight (up to 10K) | Internal/Open testing |

### Mobile Security
- Certificate pinning for API calls
- Biometric auth (Face ID / fingerprint)
- Secure storage (Keychain iOS / KeyStore Android)
- Root/jailbreak detection
- Code obfuscation (ProGuard/R8 for Android)
- No sensitive data in logs or screenshots

### Mobile Performance
- App launch time < 2 seconds
- Smooth 60fps animations
- Memory management (avoid leaks)
- Image caching and optimization
- Lazy loading for lists (FlatList/ListView)
- Bundle size: iOS < 50MB, Android < 30MB (for fast downloads)

### React Native (Expo) Recommended Stack
```
Expo SDK (managed workflow)
+ React Navigation (routing)
+ Zustand or Jotai (state)
+ TanStack Query (server state)
+ React Native Paper or Tamagui (UI)
+ Expo Notifications (push)
+ Expo SecureStore (secure storage)
+ Expo SQLite (local DB)
```

### Flutter Recommended Stack
```
Flutter SDK
+ GoRouter (navigation)
+ Riverpod or Bloc (state management)
+ Dio (HTTP client)
+ Hive or Drift (local storage)
+ Firebase Messaging (push notifications)
+ Flutter Secure Storage
```
