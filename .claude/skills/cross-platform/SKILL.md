---
name: cross-platform
description: Cross-platform and platform migration skill - triggered when building apps that target multiple platforms (web + mobile + desktop), when migrating between platforms, or when choosing a cross-platform strategy. Covers shared code, platform-specific adaptations, and migration patterns.
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, WebFetch, WebSearch, Agent
---

# Cross-Platform & Migration Skill

## When This Skill Activates
- User mentions: cross-platform, multi-platform, web + mobile, migrate to mobile
- User wants to convert web app to mobile or desktop
- User wants shared code between platforms
- During `/implement` when targeting multiple platforms

## Cross-Platform Strategy Guide

### Ask First
1. Which platforms? (Web + Mobile, Web + Desktop, All three, Mobile only)
2. How much code sharing? (Maximum -> single codebase, Some -> shared logic)
3. Native features needed? (Camera, GPS, Bluetooth, file system)
4. UI identical or platform-adapted? (Same everywhere vs native per platform)
5. Existing codebase? (Web app to extend, or greenfield)

### Strategy Options

#### Option 1: Single Codebase (Maximum sharing)
```
Flutter          -> Web + iOS + Android + Desktop (one Dart codebase)
React Native Web -> iOS + Android + Web (one React codebase)
.NET MAUI        -> iOS + Android + Windows + Mac (one C# codebase)
```
**Pros**: One team, one language, maximum code sharing (80-95%)
**Cons**: Compromises on each platform, "lowest common denominator" risk

#### Option 2: Shared Logic, Platform UI (Best of both)
```
Kotlin Multiplatform -> Shared business logic (Kotlin)
                       + SwiftUI (iOS) + Compose (Android) + React (Web)

TypeScript Monorepo  -> Shared types/logic/API client (TypeScript)
                       + Next.js (Web) + React Native (Mobile) + Electron (Desktop)
```
**Pros**: Native UI per platform, shared business logic (50-70% sharing)
**Cons**: More teams/expertise needed, more complex build system

#### Option 3: Platform-Specific with Shared API (Independent)
```
Backend API (NestJS/FastAPI)
  + Next.js (Web)
  + Swift/SwiftUI (iOS)
  + Kotlin/Compose (Android)
  + Tauri (Desktop)
```
**Pros**: Best UX per platform, independent teams
**Cons**: More code to maintain, less sharing (20-30%)

### Recommended Stacks by Scenario

**Startup (Web + Mobile, small team)**:
```
Next.js (Web) + React Native Expo (Mobile)
Shared: TypeScript types, API client, validation (Zod)
Monorepo: Turborepo or Nx
```

**Enterprise (All platforms, native feel)**:
```
Next.js (Web) + SwiftUI (iOS) + Compose (Android) + Tauri (Desktop)
Shared: API contracts (OpenAPI), business rules as API
Backend: NestJS or FastAPI
```

**Maximum Code Sharing (one team)**:
```
Flutter (Web + iOS + Android + Desktop)
Backend: Firebase or Supabase
```

### Monorepo Structure (TypeScript shared code)
```
monorepo/
  packages/
    shared/               # Shared between all platforms
      types/              # TypeScript interfaces
      api-client/         # API client (axios/ky)
      validation/         # Zod schemas
      utils/              # Helper functions
      constants/          # Shared constants
    web/                  # Next.js web app
    mobile/               # React Native Expo app
    desktop/              # Electron or Tauri app
  tools/
    eslint-config/        # Shared lint config
    tsconfig/             # Shared TS config
  package.json            # Workspace root
  turbo.json              # Turborepo config
```

### Platform Migration Patterns

#### Web -> Mobile
1. **Extract API layer**: Make sure web app talks to a clean API
2. **Extract shared logic**: Types, validation, utils -> shared package
3. **Build mobile UI**: Use React Native (if React web) or Flutter
4. **Implement native features**: Push notifications, camera, offline
5. **Test on devices**: Real devices, not just simulators
6. **Submit to stores**: App Store + Play Store

#### Web -> Desktop
1. **Wrap with Tauri/Electron**: Quickest path - wrap existing web app
2. **Add desktop features**: System tray, file access, auto-update, shortcuts
3. **Optimize for offline**: Local storage, sync when online
4. **Package & sign**: Installers for Win/Mac/Linux, code signing
5. **Distribute**: Direct download, auto-update, or app stores

#### Mobile -> Web
1. **Extract API layer**: Ensure clean API exists
2. **Build web UI**: Use React/Vue/Next.js (don't try to port mobile UI directly)
3. **Adapt UX**: Web has different patterns (sidebar vs tabs, hover vs tap)
4. **Handle auth**: Session/cookie for web (not just tokens like mobile)
5. **SEO**: Add SSR if public-facing

#### Desktop -> Web (Cloud Migration)
1. **Identify local-only features**: File system, hardware access
2. **Replace with web alternatives**: LocalStorage/IndexedDB, Web APIs
3. **Move data to cloud**: Local DB -> Cloud DB + sync
4. **Authentication**: Add proper web auth (was maybe OS-level)
5. **Progressive migration**: Run both versions during transition

### Platform-Specific Considerations

| Concern | Web | Mobile | Desktop |
|---------|-----|--------|---------|
| **Distribution** | URL (instant) | App Store (review) | Download/Store |
| **Updates** | Instant (deploy) | Store review (1-7 days) | Auto-update |
| **Offline** | Service Worker | Built-in (native DB) | Always offline capable |
| **Storage** | localStorage (5MB), IndexedDB | SQLite, Realm | Full file system |
| **Auth** | Cookies/JWT | Secure storage + biometrics | OS keychain |
| **Notifications** | Web Push (limited) | APNs/FCM (rich) | Native OS notifications |
| **Performance** | Browser limited | Native performance | Full OS access |
| **Camera/GPS** | Web APIs (permission) | Native APIs (smooth) | Limited (depends) |
| **Revenue** | Subscriptions, ads | IAP (30% cut), ads | License, subscription |

### BFF Pattern for Multi-Platform
```
iOS App    -> iOS BFF   (optimized responses for iOS)
Android    -> Android BFF (optimized for Android)
Web App    -> Web BFF   (SSR-friendly, SEO)
Desktop    -> Desktop BFF (offline-sync support)
                |
           Core API Services (shared business logic)
                |
           Database + Cache + Queue
```

Each BFF tailors:
- Response format (what fields each client needs)
- Aggregation (combine multiple API calls)
- Caching strategy (different per platform)
- Auth flow (cookies for web, tokens for mobile)
