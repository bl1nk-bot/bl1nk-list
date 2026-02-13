  # 📋 BOOKMARK APP - PROJECT SPECIFICATION

  **Document Version:** 1.0  
  **Last Updated:** February 2026  
  **Status:** Active Development

  ---

  ## 📑 Table of Contents

  1. [Project Overview](#project-overview)
  2. [Architecture](#architecture)
  3. [Technology Stack](#technology-stack)
  4. [Database Schema](#database-schema)
  5. [API Specification](#api-specification)
  6. [Client Specification](#client-specification)
  7. [Sync Specification](#sync-specification)
  8. [Security](#security)
  9. [Performance](#performance)
  10. [Deployment](#deployment)

  ---

  ## 1. Project Overview

  ### 1.1 Project Name
  **Bookmark App** - A cross-platform bookmark management application with local-first architecture and multi-device synchronization.

  ### 1.2 Vision
  Provide users with a simple, fast, and reliable way to save and manage bookmarks across all their devices without relying on cloud storage for the actual bookmark data.

  ### 1.3 Key Principles
  - **Local-First**: All bookmark data is stored locally on each device
  - **Server-Minimal**: Server only handles authentication, metadata, and sync coordination
  - **Offline-First**: Full functionality without internet connection
  - **Privacy-Focused**: User data never stored on server
  - **Cross-Platform**: Works on Web, Desktop, Mobile, and Browser Extension

  ### 1.4 Target Users
  - Power users who manage hundreds of bookmarks
  - Privacy-conscious users
  - Users who work across multiple devices
  - Developers and researchers

  ### 1.5 Success Metrics
  - Sync latency: < 5 seconds
  - Offline support: 100%
  - Data loss: 0%
  - User retention: > 80%

  ---

  ## 2. Architecture

  ### 2.1 High-Level Architecture

  ```
  ┌─────────────────────────────────────────────────────┐
  │                  Cloudflare (DNS/CDN)               │
  │                 bookmark.bl1nk.site                 │
  └──────────────────────┬──────────────────────────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
    ┌────────┐      ┌────────┐      ┌──────────┐
    │  Web   │      │  API   │      │ Workers  │
    │Vercel  │      │Vercel  │      │Cloudflare│
    └────────┘      └────┬───┘      └──────────┘
                         │
        ┌────────────────┼────────────────┐
        │                │                │
        ▼                ▼                ▼
    ┌────────┐      ┌────────┐      ┌──────────┐
    │ NeonDB │      │Upstash │      │ Prisma   │
    │  PG    │      │ Redis  │      │ Client   │
    └────────┘      └────────┘      └──────────┘
  ```

  ### 2.2 Component Overview

  | Component | Technology | Purpose |
  |-----------|-----------|---------|
  | Web App | React + Vite | Browser-based bookmark manager |
  | Desktop App | Tauri + React | Native desktop application |
  | Mobile App | Flutter | iOS & Android application |
  | Browser Extension | Manifest V3 | Quick bookmark capture |
  | Backend API | Node.js + Vercel | Authentication & sync coordination |
  | Database | PostgreSQL (NeonDB) | User data & metadata |
  | Cache | Redis (Upstash) | Session & sync state caching |
  | CDN | Cloudflare | Static asset delivery |
  | Workers | Cloudflare Workers | Background jobs & webhooks |

  ### 2.3 Data Flow

  ```
  User Action (Desktop)
         │
         ▼
  Local Storage Update
         │
         ▼
  Track Change (SyncEngine)
         │
         ▼
  Auto-Sync (30s)
         │
         ├─ Push Changes → Server
         │
         └─ Pull Changes ← Server
                │
                ▼
         Merge Changes (Conflict Resolution)
                │
                ▼
         Update Local Storage
                │
                ▼
         Notify Other Clients
                │
                ▼
         Other Clients Pull Changes
  ```

  ---

  ## 3. Technology Stack

  ### 3.1 Backend Stack

  ```
  Runtime:        Node.js 18+
  Framework:      Express.js (Vercel Functions)
  Language:       TypeScript 5.0+
  ORM:            Prisma 5.7+
  Validation:     Zod 3.22+
  Authentication: JWT + bcryptjs
  Caching:        Upstash Redis
  Database:       PostgreSQL (NeonDB)
  Deployment:     Vercel Functions
  ```

  ### 3.2 Frontend Stack (Web)

  ```
  Framework:      React 18.2+
  Build Tool:     Vite 5.0+
  Language:       TypeScript 5.0+
  State:          Zustand 4.4+
  HTTP Client:    Axios 1.6+
  Styling:        Tailwind CSS 3.3+
  Testing:        Vitest 1.0+
  Deployment:     Vercel
  ```

  ### 3.3 Desktop Stack

  ```
  Framework:      Tauri 1.5+
  Frontend:       React 18.2+
  Backend:        Rust
  Build Tool:     Vite 5.0+
  Language:       TypeScript 5.0+
  Local Storage:  SQLite (via Tauri)
  ```

  ### 3.4 Mobile Stack

  ```
  Framework:      Flutter 3.13+
  Language:       Dart 3.0+
  State:          Provider 6.0+
  HTTP Client:    Dio 5.3+
  Local Storage:  Hive 2.2+
  ```

  ### 3.5 Browser Extension Stack

  ```
  Manifest:       V3
  Framework:      React 18.2+
  Build Tool:     Vite 5.0+
  Language:       TypeScript 5.0+
  Storage:        Chrome Storage API
  ```

  ### 3.6 Package Manager & Build Tools

  ```
  Package Manager: pnpm 8.0+
  Build Tool:      Just (justfile)
  Monorepo:        pnpm workspaces
  CI/CD:           GitHub Actions
  ```

  ---

  ## 4. Database Schema

  ### 4.1 User Model

  ```typescript
  model User {
    id                    String    @id @default(cuid())
    email                 String    @unique
    username              String    @unique
    passwordHash          String?
    githubId              String?   @unique
    githubUsername        String?
    githubAvatarUrl       String?
    emailVerified         Boolean   @default(false)
    displayName           String?
    avatarUrl             String?
    bio                   String?
    theme                 String    @default("light")
    language              String    @default("en")
    createdAt             DateTime  @default(now())
    updatedAt             DateTime  @updatedAt
    lastLoginAt           DateTime?
    
    sessions              Session[]
    devices               Device[]
    syncMetadata          SyncMetadata[]
    settings              UserSettings?
  }
  ```

  ### 4.2 Session Model

  ```typescript
  model Session {
    id                String    @id @default(cuid())
    userId            String
    token             String    @unique
    refreshToken      String    @unique
    expiresAt         DateTime
    userAgent         String?
    ipAddress         String?
    deviceId          String?
    createdAt         DateTime  @default(now())
    updatedAt         DateTime  @updatedAt
    
    user              User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  }
  ```

  ### 4.3 Device Model

  ```typescript
  model Device {
    id                String    @id @default(cuid())
    userId            String
    name              String
    type              String    // "desktop" | "mobile" | "web" | "extension"
    platform          String?   // "macos" | "windows" | "linux" | "ios" | "android"
    deviceFingerprint String?
    lastSyncedAt      DateTime?
    lastSyncVersion   Int       @default(0)
    createdAt         DateTime  @default(now())
    updatedAt         DateTime  @updatedAt
    
    user              User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  }
  ```

  ### 4.4 SyncMetadata Model

  ```typescript
  model SyncMetadata {
    id                String    @id @default(cuid())
    userId            String
    currentVersion    Int       @default(0)
    lastSyncedAt      DateTime?
    clientId          String
    clientLastVersion Int       @default(0)
    clientLastSyncAt  DateTime?
    lastSyncSnapshot  Json?
    createdAt         DateTime  @default(now())
    updatedAt         DateTime  @updatedAt
    
    user              User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  }
  ```

  ### 4.5 UserSettings Model

  ```typescript
  model UserSettings {
    id                String    @id @default(cuid())
    userId            String    @unique
    autoSync          Boolean   @default(true)
    syncInterval      Int       @default(300)
    notifyOnSync      Boolean   @default(false)
    notifyOnConflict  Boolean   @default(true)
    isPublicProfile   Boolean   @default(false)
    allowDataExport   Boolean   @default(true)
    maxStorageGB      Int       @default(5)
    customSettings    Json?
    createdAt         DateTime  @default(now())
    updatedAt         DateTime  @updatedAt
    
    user              User      @relation(fields: [userId], references: [id], onDelete: Cascade)
  }
  ```

  ### 4.6 SyncLog Model

  ```typescript
  model SyncLog {
    id                String    @id @default(cuid())
    userId            String
    deviceId          String?
    action            String    // "push" | "pull" | "conflict"
    status            String    // "pending" | "completed" | "failed"
    itemsCount        Int       @default(0)
    conflictsCount    Int       @default(0)
    errorMessage      String?
    createdAt         DateTime  @default(now())
  }
  ```

  ### 4.7 Local Storage Schema (Client)

  ```typescript
  interface LocalDatabase {
    bookmarks: Bookmark[];
    tags: Tag[];
    notes: Note[];
    version: number;
    lastSyncedAt: string;
  }

  interface Bookmark {
    id: string;
    url: string;
    title: string;
    description?: string;
    favicon?: string;
    screenshot?: string;
    tags: string[];
    notes: string[];
    isArchived: boolean;
    isFavorite: boolean;
    readingTime?: number;
    createdAt: string;
    updatedAt: string;
    _version: number;
    _deviceId?: string;
  }

  interface Tag {
    id: string;
    name: string;
    color?: string;
    icon?: string;
    createdAt: string;
    updatedAt: string;
    _version: number;
  }

  interface Note {
    id: string;
    bookmarkId: string;
    content: string;
    createdAt: string;
    updatedAt: string;
    _version: number;
  }
  ```

  ---

  ## 5. API Specification

  ### 5.1 Authentication Endpoints

  #### 5.1.1 Register User
  ```
  POST /api/auth/register
  
  Request:
  {
    "email": "user@example.com",
    "username": "username",
    "password": "SecurePassword123!"
  }
  
  Response (201):
  {
    "success": true,
    "data": {
      "userId": "cuid123",
      "email": "user@example.com",
      "username": "username",
      "token": "jwt_token",
      "refreshToken": "refresh_token",
      "expiresIn": 900
    }
  }
  ```

  #### 5.1.2 Login User
  ```
  POST /api/auth/login
  
  Request:
  {
    "email": "user@example.com",
    "password": "SecurePassword123!"
  }
  
  Response (200):
  {
    "success": true,
    "data": {
      "userId": "cuid123",
      "email": "user@example.com",
      "username": "username",
      "displayName": "John Doe",
      "avatarUrl": "https://...",
      "token": "jwt_token",
      "refreshToken": "refresh_token",
      "expiresIn": 900
    }
  }
  ```

  #### 5.1.3 Refresh Token
  ```
  POST /api/auth/refresh
  
  Request:
  {
    "refreshToken": "refresh_token"
  }
  
  Response (200):
  {
    "success": true,
    "data": {
      "token": "new_jwt_token",
      "expiresIn": 900
    }
  }
  ```

  #### 5.1.4 Get Current User
  ```
  GET /api/auth/me
  Headers: Authorization: Bearer <token>
  
  Response (200):
  {
    "success": true,
    "data": {
      "id": "cuid123",
      "email": "user@example.com",
      "username": "username",
      "displayName": "John Doe",
      "avatarUrl": "https://...",
      "bio": "Bio text",
      "theme": "light",
      "language": "en",
      "emailVerified": true,
      "createdAt": "2026-02-13T00:00:00Z",
      "lastLoginAt": "2026-02-13T12:00:00Z"
    }
  }
  ```

  #### 5.1.5 Logout
  ```
  POST /api/auth/logout
  Headers: Authorization: Bearer <token>
  
  Response (200):
  {
    "success": true,
    "message": "Logged out successfully"
  }
  ```

  ### 5.2 Device Endpoints

  #### 5.2.1 Register Device
  ```
  POST /api/devices/register
  Headers: Authorization: Bearer <token>
  
  Request:
  {
    "name": "My MacBook",
    "type": "desktop",
    "platform": "macos",
    "deviceFingerprint": "device-unique-id"
  }
  
  Response (201):
  {
    "success": true,
    "data": {
      "id": "device-id",
      "userId": "user-id",
      "name": "My MacBook",
      "type": "desktop",
      "platform": "macos",
      "lastSyncedAt": null,
      "lastSyncVersion": 0,
      "createdAt": "2026-02-13T00:00:00Z"
    }
  }
  ```

  #### 5.2.2 List Devices
  ```
  GET /api/devices
  Headers: Authorization: Bearer <token>
  
  Response (200):
  {
    "success": true,
    "data": [
      {
        "id": "device-id",
        "name": "My MacBook",
        "type": "desktop",
        "platform": "macos",
        "lastSyncedAt": "2026-02-13T12:00:00Z",
        "lastSyncVersion": 5
      }
    ]
  }
  ```

  #### 5.2.3 Delete Device
  ```
  DELETE /api/devices/:id
  Headers: Authorization: Bearer <token>
  
  Response (200):
  {
    "success": true,
    "message": "Device deleted"
  }
  ```

  ### 5.3 Sync Endpoints

  #### 5.3.1 Push Changes
  ```
  POST /api/sync/push
  Headers: Authorization: Bearer <token>
  
  Request:
  {
    "deviceId": "device-id",
    "changes": [
      {
        "id": "bookmark-id",
        "type": "bookmark",
        "action": "create",
        "data": {
          "id": "bookmark-id",
          "url": "https://example.com",
          "title": "Example",
          "tags": [],
          "notes": [],
          "isArchived": false,
          "isFavorite": true,
          "createdAt": "2026-02-13T00:00:00Z",
          "updatedAt": "2026-02-13T00:00:00Z",
          "_version": 0
        },
        "timestamp": 1676246400000,
        "_version": 0
      }
    ],
    "clientVersion": 0
  }
  
  Response (200):
  {
    "success": true,
    "serverVersion": 1,
    "conflicts": [],
    "timestamp": 1676246400000
  }
  ```

  #### 5.3.2 Pull Changes
  ```
  POST /api/sync/pull
  Headers: Authorization: Bearer <token>
  
  Request:
  {
    "deviceId": "device-id",
    "clientVersion": 0,
    "lastSyncedAt": "2026-02-13T00:00:00Z"
  }
  
  Response (200):
  {
    "success": true,
    "changes": [
      {
        "id": "bookmark-id",
        "type": "bookmark",
        "action": "create",
        "data": { ... },
        "timestamp": 1676246400000,
        "deviceId": "other-device-id",
        "_version": 0
      }
    ],
    "serverVersion": 1,
    "timestamp": 1676246400000
  }
  ```

  #### 5.3.3 Get Sync Status
  ```
  GET /api/sync/status
  Headers: Authorization: Bearer <token>
  
  Response (200):
  {
    "success": true,
    "data": {
      "currentVersion": 5,
      "lastSyncedAt": "2026-02-13T12:00:00Z",
      "devices": [
        {
          "id": "device-id",
          "name": "My MacBook",
          "type": "desktop",
          "lastSyncedAt": "2026-02-13T12:00:00Z",
          "lastSyncVersion": 5
        }
      ]
    }
  }
  ```

  ### 5.4 Health Check

  #### 5.4.1 Health Status
  ```
  GET /api/health
  
  Response (200):
  {
    "status": "healthy",
    "timestamp": "2026-02-13T12:00:00Z",
    "services": {
      "database": "ok",
      "redis": "ok"
    },
    "version": "1.0.0"
  }
  ```

  ### 5.5 Error Responses

  ```
  400 Bad Request:
  {
    "error": "Validation error",
    "details": [
      {
        "field": "email",
        "message": "Invalid email"
      }
    ]
  }

  401 Unauthorized:
  {
    "error": "Unauthorized: Invalid token"
  }

  409 Conflict:
  {
    "error": "Email already registered"
  }

  500 Internal Server Error:
  {
    "error": "Internal server error"
  }
  ```

  ---

  ## 6. Client Specification

  ### 6.1 Web Client (React + Vite)

  #### 6.1.1 Features
  - ✅ User authentication (login/register)
  - ✅ Bookmark CRUD operations
  - ✅ Tag management
  - ✅ Note taking
  - ✅ Search & filter
  - ✅ Dark/Light theme
  - ✅ Responsive design
  - ✅ Offline support
  - ✅ Auto-sync

  #### 6.1.2 Pages
  - `/` - Landing page
  - `/login` - Login page
  - `/register` - Registration page
  - `/dashboard` - Main dashboard
  - `/bookmarks` - Bookmarks list
  - `/tags` - Tags management
  - `/settings` - User settings
  - `/sync-status` - Sync status

  #### 6.1.3 Components
  - `LoginForm` - Login form
  - `RegisterForm` - Registration form
  - `BookmarkCard` - Bookmark display card
  - `BookmarkList` - Bookmarks list with pagination
  - `BookmarkForm` - Create/Edit bookmark form
  - `BookmarkSearch` - Search bookmarks
  - `TagList` - Tags list
  - `TagForm` - Create/Edit tag form
  - `NoteEditor` - Note editor
  - `SyncStatus` - Sync status indicator
  - `ConflictResolver` - Conflict resolution UI

  #### 6.1.4 State Management
  - `authStore` - User authentication state
  - `bookmarkStore` - Bookmarks state
  - `syncStore` - Sync state
  - `uiStore` - UI state (theme, modals, etc)

  #### 6.1.5 Hooks
  - `useAuth()` - Authentication
  - `useBookmarks()` - Bookmark operations
  - `useSync()` - Sync operations
  - `useLocalStorage()` - Local storage persistence

  ### 6.2 Desktop Client (Tauri + React)

  #### 6.2.1 Features
  - ✅ All web features
  - ✅ Native window management
  - ✅ System tray integration
  - ✅ Keyboard shortcuts
  - ✅ File system access
  - ✅ System notifications
  - ✅ Auto-update

  #### 6.2.2 Tauri Commands
  - `get_bookmarks()` - Get all bookmarks
  - `create_bookmark()` - Create bookmark
  - `update_bookmark()` - Update bookmark
  - `delete_bookmark()` - Delete bookmark
  - `sync()` - Trigger sync
  - `get_sync_status()` - Get sync status

  #### 6.2.3 System Integration
  - Keyboard shortcut: `Cmd+Shift+B` (macOS) / `Ctrl+Shift+B` (Windows/Linux) - Quick bookmark
  - System tray menu: Open app, Sync, Settings, Quit
  - Notifications: Sync complete, Conflicts detected

  ### 6.3 Mobile Client (Flutter)

  #### 6.3.1 Features
  - ✅ User authentication
  - ✅ Bookmark CRUD
  - ✅ Tag management
  - ✅ Search & filter
  - ✅ Dark/Light theme
  - ✅ Offline support
  - ✅ Auto-sync
  - ✅ Share to app
  - ✅ Quick add from share sheet

  #### 6.3.2 Screens
  - `LoginScreen` - Login
  - `RegisterScreen` - Registration
  - `BookmarksScreen` - Bookmarks list
  - `BookmarkDetailScreen` - Bookmark details
  - `AddBookmarkScreen` - Add bookmark
  - `TagsScreen` - Tags management
  - `SettingsScreen` - Settings

  #### 6.3.3 Platform Integration
  - iOS: Share sheet integration
  - Android: Share intent handling
  - Both: Deep linking support

  ### 6.4 Browser Extension (Manifest V3)

  #### 6.4.1 Features
  - ✅ Quick bookmark save
  - ✅ Bookmark current page
  - ✅ Add tags & notes
  - ✅ View bookmarks
  - ✅ Search bookmarks
  - ✅ Sync status

  #### 6.4.2 Components
  - `Popup` - Quick bookmark popup
  - `Options` - Extension settings
  - `ContentScript` - Page content interaction
  - `Background` - Background service worker

  #### 6.4.3 Keyboard Shortcuts
  - `Ctrl+Shift+B` (Windows/Linux) / `Cmd+Shift+B` (macOS) - Quick bookmark

  ---

  ## 7. Sync Specification

  ### 7.1 Sync Architecture

  ```
  Client 1 (Desktop)
       │
       ├─ Local Storage (Bookmarks, Tags, Notes)
       ├─ SyncEngine (Track changes)
       └─ SyncService (API communication)
            │
            ├─ Push: Send changes to server
            │
            └─ Pull: Get changes from other clients
                 │
                 ▼
            Server (Minimal)
                 │
                 ├─ Track versions
                 ├─ Coordinate devices
                 └─ Detect conflicts
                 │
                 ▼
            Client 2 (Mobile)
                 │
                 ├─ Pull: Get changes
                 ├─ Merge: Apply changes
                 └─ Sync: Update local storage
  ```

  ### 7.2 Sync Flow

  1. **Local Change**: User creates/edits bookmark
   - SyncEngine tracks change
   - Change added to pendingChanges array

  2. **Auto-Sync Trigger** (every 30 seconds)
   - Check if pendingChanges exist
   - If yes, start sync

  3. **Push Phase**
   - Send pendingChanges to server
   - Server increments version
   - Server stores sync metadata

  4. **Pull Phase**
   - Request changes from server
   - Get changes from other devices
   - Receive new server version

  5. **Merge Phase**
   - Apply remote changes to local storage
   - Detect conflicts
   - Resolve conflicts (auto or manual)

  6. **Persist Phase**
   - Save updated local storage
   - Clear pendingChanges
   - Update sync metadata

  ### 7.3 Conflict Resolution

  #### 7.3.1 Conflict Detection
  - Same bookmark modified on 2+ devices
  - Detected by version mismatch
  - Timestamp comparison

  #### 7.3.2 Resolution Strategies
  - **Last-Write-Wins**: Server timestamp wins
  - **Manual**: User chooses which version to keep
  - **Merge**: Combine changes (future enhancement)

  #### 7.3.3 Conflict UI
  - Show both versions
  - User selects which to keep
  - Can preview differences

  ### 7.4 Sync Metadata

  ```typescript
  {
    userId: "user-id",
    clientId: "device-id",
    currentVersion: 5,
    clientLastVersion: 5,
    clientLastSyncAt: "2026-02-13T12:00:00Z",
    lastSyncedAt: "2026-02-13T12:00:00Z"
  }
  ```

  ### 7.5 Sync Intervals

  | Event | Interval |
  |-------|----------|
  | Auto-sync | 30 seconds |
  | Manual sync | On demand |
  | Conflict check | Every sync |
  | Session refresh | 15 minutes |
  | Device cleanup | Daily |

  ---

  ## 8. Security

  ### 8.1 Authentication

  - **Method**: JWT (JSON Web Tokens)
  - **Access Token**: 15 minutes expiration
  - **Refresh Token**: 7 days expiration
  - **Storage**: Secure HTTP-only cookies (web), Secure storage (mobile/desktop)

  ### 8.2 Password Security

  - **Hashing**: bcryptjs with 10 salt rounds
  - **Requirements**:
    - Minimum 8 characters
    - Uppercase letter
    - Lowercase letter
    - Number
    - Special character

  ### 8.3 Data Encryption

  - **In Transit**: HTTPS/TLS 1.3+
  - **At Rest**: Database encryption (NeonDB)
  - **Local Storage**: Device encryption (OS-level)

  ### 8.4 CORS Policy

  ```
  Allowed Origins:
  - https://bookmark.bl1nk.site
  - https://api.bookmark.bl1nk.site
  - http://localhost:3000 (dev)
  - http://localhost:5173 (dev)
  ```

  ### 8.5 Rate Limiting

  | Endpoint | Limit | Window |
  |----------|-------|--------|
  | /api/auth/login | 5 | 15 minutes |
  | /api/auth/register | 3 | 1 hour |
  | /api/sync/push | 100 | 1 minute |
  | /api/sync/pull | 100 | 1 minute |

  ### 8.6 Data Privacy

  - ✅ No bookmark data stored on server
  - ✅ Only metadata stored (user, device, sync info)
  - ✅ User can export all data
  - ✅ User can delete account (all data removed)
  - ✅ GDPR compliant

  ---

  ## 9. Performance

  ### 9.1 Performance Targets

  | Metric | Target |
  |--------|--------|
  | Page load | < 2s |
  | Sync latency | < 5s |
  | Search response | < 100ms |
  | API response | < 500ms |
  | Database query | < 100ms |

  ### 9.2 Optimization Strategies

  - **Frontend**:
    - Code splitting
    - Lazy loading
    - Image optimization
    - CSS minification
    - JavaScript minification

  - **Backend**:
    - Database indexing
    - Redis caching
    - Connection pooling
    - Query optimization

  - **Network**:
    - Gzip compression
    - CDN caching
    - HTTP/2 support
    - Cloudflare optimization

  ### 9.3 Caching Strategy

  | Resource | TTL | Strategy |
  |----------|-----|----------|
  | User session | 15 minutes | Redis |
  | Sync metadata | 1 hour | Redis |
  | Sync status | 1 hour | Redis |
  | Static assets | 1 year | CDN |
  | API responses | 5 minutes | Client-side |

  ### 9.4 Database Optimization

  ```sql
  -- Indexes
  CREATE INDEX idx_user_email ON users(email);
  CREATE INDEX idx_session_token ON sessions(token);
  CREATE INDEX idx_device_user ON devices(user_id);
  CREATE INDEX idx_sync_metadata_user ON sync_metadata(user_id);
  CREATE INDEX idx_sync_log_user ON sync_logs(user_id);
  ```

  ---

  ## 10. Deployment

  ### 10.1 Environments

  | Environment | Purpose | Domain |
  |-------------|---------|--------|
  | Development | Local testing | localhost |
  | Staging | Pre-production testing | staging.bookmark.bl1nk.site |
  | Production | Live application | bookmark.bl1nk.site |

  ### 10.2 Deployment Platforms

  | Component | Platform | Region |
  |-----------|----------|--------|
  | Web App | Vercel | Global CDN |
  | Backend API | Vercel Functions | Global |
  | Database | NeonDB | US-East |
  | Cache | Upstash | Global |
  | CDN | Cloudflare | Global |
  | Workers | Cloudflare Workers | Global |

  ### 10.3 CI/CD Pipeline

  ```
  Git Push
    │
    ├─ Run Tests
    │   ├─ Unit tests
    │   ├─ Integration tests
    │   └─ E2E tests
    │
    ├─ Lint & Format
    │   ├─ ESLint
    │   ├─ Prettier
    │   └─ Type check
    │
    ├─ Build
    │   ├─ Backend build
    │   ├─ Frontend build
    │   └─ Desktop build
    │
    └─ Deploy
        ├─ Deploy to staging
        ├─ Run smoke tests
        └─ Deploy to production
  ```

  ### 10.4 Deployment Checklist

  - [ ] All tests passing
  - [ ] Code review approved
  - [ ] Environment variables configured
  - [ ] Database migrations ready
  - [ ] Backup created
  - [ ] Rollback plan documented
  - [ ] Monitoring alerts configured
  - [ ] Deployment window scheduled

  ### 10.5 Monitoring & Logging

  - **Logging**: Vercel logs, Cloudflare logs
  - **Monitoring**: Uptime monitoring, Error tracking
  - **Alerts**: Email notifications on errors
  - **Metrics**: Response time, Error rate, Sync success rate

  ---

  ## 11. Development Roadmap

  ### Phase 1: MVP (Current)
  - ✅ User authentication
  - ✅ Local bookmark storage
  - ✅ Basic sync
  - ✅ Web client
  - ⏳ Desktop client
  - ⏳ Mobile client
  - ⏳ Browser extension

  ### Phase 2: Enhancement
  - [ ] Advanced search
  - [ ] Collections/Folders
  - [ ] Sharing bookmarks
  - [ ] Public profiles
  - [ ] Social features

  ### Phase 3: Integration
  - [ ] Pocket import
  - [ ] Raindrop import
  - [ ] Browser sync
  - [ ] Calendar integration
  - [ ] Slack integration

  ### Phase 4: AI Features
  - [ ] Auto-tagging
  - [ ] Smart recommendations
  - [ ] Content summarization
  - [ ] Duplicate detection

  ---

  ## 12. Glossary

  | Term | Definition |
  |------|-----------|
  | **SyncEngine** | Client-side logic for tracking and merging changes |
  | **SyncService** | API client for sync operations |
  | **StorageManager** | Persistence layer for local storage |
  | **SyncCoordinator** | Orchestrates sync between engine and service |
  | **Conflict** | When same item modified on 2+ devices |
  | **Version** | Sync version number for tracking changes |
  | **Device** | Client instance (desktop, mobile, web, extension) |
  | **Metadata** | Non-bookmark data (user, device, sync info) |

  ---

  ## 13. References

  - [Prisma Documentation](https://www.prisma.io/docs/)
  - [Vercel Functions](https://vercel.com/docs/functions/serverless-functions)
  - [Cloudflare Workers](https://developers.cloudflare.com/workers/)
  - [NeonDB Documentation](https://neon.tech/docs/)
  - [Upstash Redis](https://upstash.com/docs/)
  - [React Documentation](https://react.dev/)
  - [Tauri Documentation](https://tauri.app/docs/)
  - [Flutter Documentation](https://flutter.dev/docs/)

  ---

  **Document End**

  *For questions or updates, please contact the development team.*
