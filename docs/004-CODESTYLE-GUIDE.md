  # 📝 BOOKMARK APP - CODE STYLE GUIDE

  **Document Version:** 1.0  
  **Last Updated:** February 2026  
  **Status:** Active Development

  ---

  ## 📑 Table of Contents

  1. [File & Folder Naming](#file--folder-naming)
  2. [Code Comments](#code-comments)
  3. [TypeScript Style](#typescript-style)
  4. [React Component Style](#react-component-style)
  5. [API Endpoint Style](#api-endpoint-style)
  6. [Database Query Style](#database-query-style)
  7. [Error Handling](#error-handling)
  8. [Testing Style](#testing-style)
  9. [Git Workflow](#git-workflow)
  10. [Documentation Standards](#documentation-standards)

  ---

  ## 1. File & Folder Naming

  ### 1.1 Naming Conventions

  **Folder Names** (ใช้ขีดกลาง)
  ```
  ✅ GOOD:
  src/
  ├─ api-handlers/
  ├─ sync-engine/
  ├─ local-storage/
  ├─ ui-components/
  ├─ state-management/
  └─ utils-helpers/

  ❌ BAD:
  src/
  ├─ ApiHandlers/
  ├─ SyncEngine/
  ├─ LocalStorage/
  ├─ UIComponents/
  └─ StateManagement/
  ```

  **File Names** (ใช้ตัวเล็ก)
  ```
  ✅ GOOD:
  src/
  ├─ sync-engine/
  │  ├─ sync-engine.ts
  │  ├─ sync-service.ts
  │  └─ storage-manager.ts
  ├─ api-handlers/
  │  ├─ auth-handler.ts
  │  ├─ sync-handler.ts
  │  └─ device-handler.ts
  └─ ui-components/
     ├─ bookmark-card.tsx
     ├─ bookmark-list.tsx
     └─ bookmark-form.tsx

  ❌ BAD:
  src/
  ├─ SyncEngine/
  │  ├─ SyncEngine.ts
  │  ├─ SyncService.ts
  │  └─ StorageManager.ts
  ├─ APIHandlers/
  │  ├─ AuthHandler.ts
  │  ├─ SyncHandler.ts
  │  └─ DeviceHandler.ts
  └─ UIComponents/
     ├─ BookmarkCard.tsx
     ├─ BookmarkList.tsx
     └─ BookmarkForm.tsx
  ```

  **Document Names** (ตัวเลขตามด้วยตัวใหญ่)
  ```
  ✅ GOOD:
  docs/
  ├─ 001-SPEC.md
  ├─ 002-PRODUCT.md
  ├─ 003-ARCHITECTURE.md
  ├─ 004-CODESTYLE-GUIDE.md
  ├─ 005-API-GUIDE.md
  └─ 006-CONTRIBUTING.md

  ❌ BAD:
  docs/
  ├─ spec.md
  ├─ product.md
  ├─ architecture.md
  ├─ codestyle-guide.md
  ├─ api-guide.md
  └─ contributing.md
  ```

  **Secret/Environment Variables** (ตัวใหญ่เท่านั้น)
  ```
  ✅ GOOD:
  DATABASE_URL=postgresql://...
  REDIS_URL=redis://...
  JWT_SECRET=your-secret-key
  JWT_REFRESH_SECRET=your-refresh-secret
  GITHUB_CLIENT_ID=...
  GITHUB_CLIENT_SECRET=...
  GOOGLE_CLIENT_ID=...
  GOOGLE_CLIENT_SECRET=...
  SENTRY_DSN=...
  API_BASE_URL=...
  UPSTASH_REDIS_URL=...
  UPSTASH_REDIS_TOKEN=...

  ❌ BAD:
  databaseUrl=postgresql://...
  redisUrl=redis://...
  jwtSecret=your-secret-key
  githubClientId=...
  ```

  ### 1.2 File Structure

  ```
  bookmark-app/
  ├─ packages/
  │  ├─ shared/
  │  │  ├─ src/
  │  │  │  ├─ sync-engine/
  │  │  │  │  ├─ sync-engine.ts
  │  │  │  │  ├─ sync-service.ts
  │  │  │  │  └─ storage-manager.ts
  │  │  │  ├─ types/
  │  │  │  │  ├─ bookmark.ts
  │  │  │  │  ├─ sync.ts
  │  │  │  │  └─ user.ts
  │  │  │  ├─ utils/
  │  │  │  │  ├─ validators.ts
  │  │  │  │  ├─ formatters.ts
  │  │  │  │  └─ helpers.ts
  │  │  │  └─ index.ts
  │  │  ├─ package.json
  │  │  └─ tsconfig.json
  │  │
  │  ├─ web/
  │  │  ├─ src/
  │  │  │  ├─ pages/
  │  │  │  │  ├─ login-page.tsx
  │  │  │  │  ├─ dashboard-page.tsx
  │  │  │  │  └─ settings-page.tsx
  │  │  │  ├─ components/
  │  │  │  │  ├─ bookmark-card.tsx
  │  │  │  │  ├─ bookmark-list.tsx
  │  │  │  │  └─ sync-indicator.tsx
  │  │  │  ├─ hooks/
  │  │  │  │  ├─ use-auth.ts
  │  │  │  │  ├─ use-bookmarks.ts
  │  │  │  │  └─ use-sync.ts
  │  │  │  ├─ stores/
  │  │  │  │  ├─ auth-store.ts
  │  │  │  │  ├─ bookmark-store.ts
  │  │  │  │  └─ sync-store.ts
  │  │  │  ├─ services/
  │  │  │  │  ├─ api-client.ts
  │  │  │  │  └─ local-storage.ts
  │  │  │  ├─ styles/
  │  │  │  │  ├─ globals.css
  │  │  │  │  └─ variables.css
  │  │  │  ├─ app.tsx
  │  │  │  └─ main.tsx
  │  │  ├─ public/
  │  │  ├─ package.json
  │  │  ├─ vite.config.ts
  │  │  └─ tsconfig.json
  │  │
  │  ├─ desktop/
  │  │  ├─ src-tauri/
  │  │  │  ├─ main.rs
  │  │  │  └─ lib.rs
  │  │  ├─ src/
  │  │  │  └─ (same as web)
  │  │  ├─ tauri.conf.json
  │  │  └─ package.json
  │  │
  │  └─ mobile/
  │     ├─ lib/
  │     │  ├─ screens/
  │     │  ├─ widgets/
  │     │  ├─ services/
  │     │  ├─ providers/
  │     │  └─ main.dart
  │     ├─ pubspec.yaml
  │     └─ analysis_options.yaml
  │
  ├─ api/
  │  ├─ src/
  │  │  ├─ handlers/
  │  │  │  ├─ auth-handler.ts
  │  │  │  ├─ sync-handler.ts
  │  │  │  └─ device-handler.ts
  │  │  ├─ services/
  │  │  │  ├─ auth-service.ts
  │  │  │  ├─ sync-service.ts
  │  │  │  └─ device-service.ts
  │  │  ├─ middleware/
  │  │  │  ├─ auth-middleware.ts
  │  │  │  ├─ rate-limiter.ts
  │  │  │  └─ error-handler.ts
  │  │  ├─ utils/
  │  │  │  ├─ validators.ts
  │  │  │  ├─ jwt-utils.ts
  │  │  │  └─ error-utils.ts
  │  │  ├─ types/
  │  │  │  ├─ api.ts
  │  │  │  ├─ database.ts
  │  │  │  └─ errors.ts
  │  │  └─ index.ts
  │  ├─ prisma/
  │  │  ├─ schema.prisma
  │  │  └─ migrations/
  │  ├─ package.json
  │  ├─ tsconfig.json
  │  └─ vercel.json
  │
  ├─ docs/
  │  ├─ 001-SPEC.md
  │  ├─ 002-PRODUCT.md
  │  ├─ 003-ARCHITECTURE.md
  │  ├─ 004-CODESTYLE-GUIDE.md
  │  ├─ 005-API-GUIDE.md
  │  └─ 006-CONTRIBUTING.md
  │
  ├─ .github/
  │  └─ workflows/
  │     ├─ test.yml
  │     ├─ lint.yml
  │     └─ deploy.yml
  │
  ├─ .gitignore
  ├─ .env.example
  ├─ pnpm-workspace.yaml
  ├─ package.json
  └─ README.md
  ```

  ---

  ## 2. Code Comments

  ### 2.1 Comment Tags

  **TODO** - ต้องกำลังทำหรือต้องทำต่อ
  ```typescript
  // TODO: ต้องเพิ่มการตรวจสอบ email ซ้ำ
  async function registerUser(email: string, password: string) {
    // TODO: ต้องส่ง verification email
    const user = await db.user.create({
      data: { email, password: hashPassword(password) }
    });
    return user;
  }
  ```

  **FIX** - ต้องกลับมาแก้
  ```typescript
  // FIX: ต้องแก้ race condition เมื่อ sync พร้อมกัน
  async function syncBookmarks(userId: string) {
    const changes = await getChanges(userId);
    // FIX: ต้องเพิ่ม retry logic สำหรับ network error
    await pushChanges(changes);
  }
  ```

  **MOCK** - ส่วนที่ยังเป็น stub หรือ mock
  ```typescript
  // MOCK: ยังใช้ mock data ต้องแทนที่ด้วย real API
  async function getBookmarks(userId: string): Promise<Bookmark[]> {
    // MOCK: ต้องเรียก API จริง
    return [
      { id: '1', url: 'https://example.com', title: 'Example' }
    ];
  }
  ```

  **DOC** - ส่วนที่อ้างอิงและต้องลงเอกสาร
  ```typescript
  // DOC: ดูรายละเอียดใน 005-API-GUIDE.md
  // DOC: Sync algorithm อธิบายใน 003-ARCHITECTURE.md section 6
  async function mergeSyncChanges(
    localChanges: SyncChange[],
    remoteChanges: SyncChange[]
  ): Promise<SyncChange[]> {
    // Implementation
  }
  ```

  **TEST** - ส่วน test
  ```typescript
  // TEST: ต้องเพิ่ม test case สำหรับ conflict resolution
  describe('SyncEngine', () => {
    // TEST: ต้องเพิ่ม test สำหรับ offline sync
    it('should queue changes when offline', () => {
      // Test implementation
    });
  });
  ```

  ### 2.2 Comment Style

  **ใช้ภาษาไทยสำหรับรายละเอียด**
  ```typescript
  ✅ GOOD:
  // ตรวจสอบว่า user มี permission ในการแก้ไข bookmark
  if (!canEditBookmark(userId, bookmarkId)) {
    throw new UnauthorizedError('ไม่มีสิทธิ์แก้ไข bookmark นี้');
  }

  // ส่งข้อมูลการซิงค์ไปยังอุปกรณ์อื่น
  await notifyOtherDevices(userId, changes);

  ❌ BAD:
  // Check if user can edit bookmark
  if (!canEditBookmark(userId, bookmarkId)) {
    throw new UnauthorizedError('No permission to edit this bookmark');
  }
  ```

  **Block Comments**
  ```typescript
  /**
   * ตรวจสอบและรวมการเปลี่ยนแปลงจากอุปกรณ์อื่น
   * 
   * @param localChanges - การเปลี่ยนแปลงจากอุปกรณ์ปัจจุบัน
   * @param remoteChanges - การเปลี่ยนแปลงจากอุปกรณ์อื่น
   * @returns การเปลี่ยนแปลงที่รวมแล้ว
   * 
   * DOC: ดูรายละเอียดใน 003-ARCHITECTURE.md section 6.2
   */
  async function mergeChanges(
    localChanges: SyncChange[],
    remoteChanges: SyncChange[]
  ): Promise<SyncChange[]> {
    // Implementation
  }
  ```

  **Inline Comments**
  ```typescript
  // ต้องตรวจสอบ version เพื่อหา conflict
  if (local._version !== remote._version) {
    conflicts.push({ id, local, remote });
  }

  // ใช้ timestamp ของ server เป็น source of truth
  const winner = local.updatedAt > remote.updatedAt ? local : remote;
  ```

  ---

  ## 3. TypeScript Style

  ### 3.1 Type Definitions

  ```typescript
  ✅ GOOD:
  // ใช้ interface สำหรับ object types
  interface Bookmark {
    id: string;
    url: string;
    title: string;
    description?: string;
    tags: string[];
    createdAt: Date;
    updatedAt: Date;
    _version: number;
  }

  // ใช้ type สำหรับ union types
  type SyncAction = 'create' | 'update' | 'delete';
  type SyncStatus = 'pending' | 'completed' | 'failed';

  // ใช้ enum สำหรับ constants
  enum DeviceType {
    Desktop = 'desktop',
    Mobile = 'mobile',
    Web = 'web',
    Extension = 'extension'
  }

  ❌ BAD:
  // ไม่ใช้ any
  function processBookmark(bookmark: any): any {
    return bookmark;
  }

  // ไม่ใช้ implicit any
  function getUser(id) {
    return db.user.findUnique({ where: { id } });
  }
  ```

  ### 3.2 Function Style

  ```typescript
  ✅ GOOD:
  // ใช้ arrow function
  const createBookmark = async (
    userId: string,
    data: CreateBookmarkInput
  ): Promise<Bookmark> => {
    // Implementation
  };

  // ใช้ explicit return type
  async function syncBookmarks(userId: string): Promise<SyncResult> {
    // Implementation
  }

  // ใช้ destructuring
  const { id, title, url } = bookmark;

  ❌ BAD:
  // ไม่ใช้ function declaration
  function createBookmark(userId, data) {
    // Implementation
  }

  // ไม่ใช้ implicit return type
  const syncBookmarks = async (userId) => {
    // Implementation
  };
  ```

  ### 3.3 Class Style

  ```typescript
  ✅ GOOD:
  class SyncEngine {
    // ใช้ private สำหรับ internal state
    private pendingChanges: SyncChange[] = [];
    private version: number = 0;

    // ใช้ public สำหรับ public methods
    public async sync(): Promise<void> {
      // Implementation
    }

    // ใช้ private สำหรับ helper methods
    private validateChanges(changes: SyncChange[]): boolean {
      // Implementation
      return true;
    }

    // ใช้ getter/setter สำหรับ properties
    get currentVersion(): number {
      return this.version;
    }
  }

  ❌ BAD:
  class SyncEngine {
    // ไม่ใช้ public properties
    public pendingChanges = [];

    // ไม่มี access modifiers
    sync() {
      // Implementation
    }
  }
  ```

  ---

  ## 4. React Component Style

  ### 4.1 Component Structure

  ```typescript
  ✅ GOOD:
  // ใช้ functional components
  interface BookmarkCardProps {
    bookmark: Bookmark;
    onEdit: (id: string) => void;
    onDelete: (id: string) => void;
  }

  export const BookmarkCard: React.FC<BookmarkCardProps> = ({
    bookmark,
    onEdit,
    onDelete
  }) => {
    // ประกาศ hooks ที่ด้านบน
    const [isHovered, setIsHovered] = useState(false);

    // ประกาศ handlers
    const handleEdit = () => {
      onEdit(bookmark.id);
    };

    const handleDelete = () => {
      onDelete(bookmark.id);
    };

    // Render
    return (
      <div
        className="bookmark-card"
        onMouseEnter={() => setIsHovered(true)}
        onMouseLeave={() => setIsHovered(false)}
      >
        <h3>{bookmark.title}</h3>
        <p>{bookmark.description}</p>
        {isHovered && (
          <div className="actions">
            <button onClick={handleEdit}>แก้ไข</button>
            <button onClick={handleDelete}>ลบ</button>
          </div>
        )}
      </div>
    );
  };

  ❌ BAD:
  // ไม่ใช้ class components
  class BookmarkCard extends React.Component {
    render() {
      return <div>{this.props.bookmark.title}</div>;
    }
  }

  // ไม่ใช้ inline styles
  const BookmarkCard = ({ bookmark }) => (
    <div style={{ color: 'red', fontSize: '16px' }}>
      {bookmark.title}
    </div>
  );
  ```

  ### 4.2 Hooks Usage

  ```typescript
  ✅ GOOD:
  // ใช้ custom hooks สำหรับ shared logic
  const useBookmarks = () => {
    const [bookmarks, setBookmarks] = useState<Bookmark[]>([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState<string | null>(null);

    useEffect(() => {
      // ดึง bookmarks
      loadBookmarks();
    }, []);

    const loadBookmarks = async () => {
      try {
        setLoading(true);
        const data = await api.getBookmarks();
        setBookmarks(data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    return { bookmarks, loading, error, loadBookmarks };
  };

  // ใช้ useCallback สำหรับ memoized functions
  const handleDelete = useCallback(async (id: string) => {
    await api.deleteBookmark(id);
    await loadBookmarks();
  }, []);

  // ใช้ useMemo สำหรับ expensive computations
  const filteredBookmarks = useMemo(
    () => bookmarks.filter(b => b.tags.includes(selectedTag)),
    [bookmarks, selectedTag]
  );

  ❌ BAD:
  // ไม่ใช้ hooks ในลูป
  for (let i = 0; i < 10; i++) {
    useState(0); // ❌ WRONG
  }

  // ไม่ใช้ hooks ในเงื่อนไข
  if (condition) {
    useEffect(() => {}, []); // ❌ WRONG
  }
  ```

  ### 4.3 Component Organization

  ```typescript
  // ไฟล์: src/components/bookmark-card.tsx
  
  import React, { useState, useCallback } from 'react';
  import { Bookmark } from '@bookmark/shared';
  import './bookmark-card.css';

  // ประกาศ types
  interface BookmarkCardProps {
    bookmark: Bookmark;
    onEdit: (id: string) => void;
    onDelete: (id: string) => void;
  }

  // ประกาศ constants
  const CARD_ACTIONS = ['แก้ไข', 'ลบ', 'เก็บถาวร'] as const;

  // ประกาศ component
  export const BookmarkCard: React.FC<BookmarkCardProps> = ({
    bookmark,
    onEdit,
    onDelete
  }) => {
    // Hooks
    const [isHovered, setIsHovered] = useState(false);

    // Handlers
    const handleEdit = useCallback(() => {
      onEdit(bookmark.id);
    }, [bookmark.id, onEdit]);

    const handleDelete = useCallback(() => {
      onDelete(bookmark.id);
    }, [bookmark.id, onDelete]);

    // Render
    return (
      <div className="bookmark-card">
        {/* Content */}
      </div>
    );
  };

  // Export
  export default BookmarkCard;
  ```

  ---

  ## 5. API Endpoint Style

  ### 5.1 Handler Style

  ```typescript
  // ไฟล์: api/src/handlers/auth-handler.ts

  import { Request, Response } from 'express';
  import { AuthService } from '../services/auth-service';
  import { validateEmail, validatePassword } from '../utils/validators';

  // TODO: ต้องเพิ่ม rate limiting
  export const registerHandler = async (
    req: Request,
    res: Response
  ): Promise<void> => {
    try {
      // ตรวจสอบ input
      const { email, password, username } = req.body;

      if (!validateEmail(email)) {
        res.status(400).json({ error: 'Email ไม่ถูกต้อง' });
        return;
      }

      if (!validatePassword(password)) {
        res.status(400).json({ error: 'รหัสผ่านไม่ถูกต้อง' });
        return;
      }

      // เรียก service
      const result = await AuthService.register(email, password, username);

      // ส่ง response
      res.status(201).json({
        success: true,
        data: result
      });
    } catch (error) {
      // FIX: ต้องเพิ่ม error logging
      res.status(500).json({ error: 'Internal server error' });
    }
  };

  // DOC: ดูรายละเอียด endpoint ใน 005-API-GUIDE.md
  export const loginHandler = async (
    req: Request,
    res: Response
  ): Promise<void> => {
    // Implementation
  };
  ```

  ### 5.2 Service Style

  ```typescript
  // ไฟล์: api/src/services/auth-service.ts

  import { prisma } from '../db';
  import { hashPassword, verifyPassword } from '../utils/password-utils';
  import { generateTokens } from '../utils/jwt-utils';

  export class AuthService {
    /**
     * ลงทะเบียนผู้ใช้ใหม่
     * 
     * @param email - อีเมลของผู้ใช้
     * @param password - รหัสผ่าน
     * @param username - ชื่อผู้ใช้
     * @returns ข้อมูลผู้ใช้และ tokens
     * 
     * TODO: ต้องส่ง verification email
     */
    static async register(
      email: string,
      password: string,
      username: string
    ) {
      // ตรวจสอบว่า email มีอยู่แล้ว
      const existingUser = await prisma.user.findUnique({
        where: { email }
      });

      if (existingUser) {
        throw new Error('Email นี้ถูกใช้แล้ว');
      }

      // แฮช password
      const passwordHash = await hashPassword(password);

      // สร้าง user
      const user = await prisma.user.create({
        data: {
          email,
          username,
          passwordHash
        }
      });

      // สร้าง tokens
      const { accessToken, refreshToken } = generateTokens(user.id);

      return {
        userId: user.id,
        email: user.email,
        username: user.username,
        accessToken,
        refreshToken
      };
    }

    /**
     * ล็อกอิน
     * 
     * @param email - อีเมลของผู้ใช้
     * @param password - รหัสผ่าน
     * @returns ข้อมูลผู้ใช้และ tokens
     * 
     * FIX: ต้องเพิ่ม rate limiting
     */
    static async login(email: string, password: string) {
      // Implementation
    }
  }
  ```

  ---

  ## 6. Database Query Style

  ### 6.1 Prisma Query Style

  ```typescript
  ✅ GOOD:
  // ใช้ select เพื่อ optimize query
  const user = await prisma.user.findUnique({
    where: { id: userId },
    select: {
      id: true,
      email: true,
      username: true,
      displayName: true,
      // ไม่ดึง passwordHash
    }
  });

  // ใช้ include สำหรับ relations
  const userWithDevices = await prisma.user.findUnique({
    where: { id: userId },
    include: {
      devices: true,
      sessions: {
        where: { expiresAt: { gt: new Date() } }
      }
    }
  });

  // ใช้ batch queries
  const [users, devices, syncLogs] = await Promise.all([
    prisma.user.findMany(),
    prisma.device.findMany(),
    prisma.syncLog.findMany()
  ]);

  ❌ BAD:
  // ไม่ดึง unnecessary fields
  const user = await prisma.user.findUnique({
    where: { id: userId }
    // ดึง passwordHash ด้วย
  });

  // ไม่ใช้ nested queries
  const users = await prisma.user.findMany();
  for (const user of users) {
    const devices = await prisma.device.findMany({
      where: { userId: user.id }
    });
  }
  ```

  ### 6.2 Transaction Style

  ```typescript
  // ใช้ transaction สำหรับ atomic operations
  const result = await prisma.$transaction(async (tx) => {
    // ตรวจสอบ version
    const current = await tx.syncMetadata.findUnique({
      where: { userId_clientId: { userId, clientId } }
    });

    if (current.currentVersion !== clientVersion) {
      throw new Error('Version mismatch');
    }

    // อัพเดท version
    const updated = await tx.syncMetadata.update({
      where: { userId_clientId: { userId, clientId } },
      data: { currentVersion: clientVersion + 1 }
    });

    // บันทึก sync log
    await tx.syncLog.create({
      data: {
        userId,
        deviceId: clientId,
        action: 'push',
        status: 'completed',
        itemsCount: changes.length
      }
    });

    return updated;
  });
  ```

  ---

  ## 7. Error Handling

  ### 7.1 Custom Error Classes

  ```typescript
  // ไฟล์: api/src/types/errors.ts

  /**
   * Base error class สำหรับ custom errors
   */
  export class AppError extends Error {
    constructor(
      public statusCode: number,
      public message: string,
      public code?: string
    ) {
      super(message);
      this.name = this.constructor.name;
    }
  }

  // ต้องใช้ error classes เหล่านี้
  export class ValidationError extends AppError {
    constructor(message: string) {
      super(400, message, 'VALIDATION_ERROR');
    }
  }

  export class UnauthorizedError extends AppError {
    constructor(message: string = 'Unauthorized') {
      super(401, message, 'UNAUTHORIZED');
    }
  }

  export class ForbiddenError extends AppError {
    constructor(message: string = 'Forbidden') {
      super(403, message, 'FORBIDDEN');
    }
  }

  export class NotFoundError extends AppError {
    constructor(message: string = 'Not found') {
      super(404, message, 'NOT_FOUND');
    }
  }

  export class ConflictError extends AppError {
    constructor(message: string = 'Conflict') {
      super(409, message, 'CONFLICT');
    }
  }

  export class InternalServerError extends AppError {
    constructor(message: string = 'Internal server error') {
      super(500, message, 'INTERNAL_SERVER_ERROR');
    }
  }
  ```

  ### 7.2 Error Handling Pattern

  ```typescript
  ✅ GOOD:
  // ใช้ try-catch กับ specific errors
  try {
    const user = await getUserById(userId);
    if (!user) {
      throw new NotFoundError('ไม่พบผู้ใช้');
    }
    return user;
  } catch (error) {
    if (error instanceof AppError) {
      // Handle app errors
      logger.warn(`App error: ${error.message}`);
      throw error;
    } else {
      // Handle unexpected errors
      logger.error('Unexpected error:', error);
      throw new InternalServerError();
    }
  }

  // ใช้ error middleware
  app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
    if (err instanceof AppError) {
      res.status(err.statusCode).json({
        error: err.message,
        code: err.code
      });
    } else {
      res.status(500).json({
        error: 'Internal server error'
      });
    }
  });

  ❌ BAD:
  // ไม่ใช้ generic error handling
  try {
    // Something
  } catch (e) {
    res.status(500).json({ error: e });
  }
  ```

  ---

  ## 8. Testing Style

  ### 8.1 Test File Naming

  ```
  ✅ GOOD:
  src/
  ├─ sync-engine/
  │  ├─ sync-engine.ts
  │  ├─ sync-engine.test.ts
  │  ├─ sync-service.ts
  │  └─ sync-service.test.ts
  ├─ api-handlers/
  │  ├─ auth-handler.ts
  │  └─ auth-handler.test.ts

  ❌ BAD:
  src/
  ├─ sync-engine/
  │  ├─ sync-engine.ts
  │  └─ test-sync-engine.ts
  ├─ api-handlers/
  │  ├─ auth-handler.ts
  │  └─ AuthHandlerTest.ts
  ```

  ### 8.2 Test Structure

  ```typescript
  // ไฟล์: src/sync-engine/sync-engine.test.ts

  import { describe, it, expect, beforeEach, afterEach } from 'vitest';
  import { SyncEngine } from './sync-engine';

  /**
   * TEST: ต้องเพิ่ม test cases สำหรับ conflict resolution
   */
  describe('SyncEngine', () => {
    let engine: SyncEngine;

    beforeEach(() => {
      // ตั้งค่า test environment
      engine = new SyncEngine('test-user', 'test-device');
    });

    afterEach(() => {
      // ทำความสะอาด
      engine.destroy();
    });

    describe('createBookmark', () => {
      it('ต้องสร้าง bookmark ใหม่', () => {
        const bookmark = engine.createBookmark({
          url: 'https://example.com',
          title: 'Example'
        });

        expect(bookmark.id).toBeDefined();
        expect(bookmark.url).toBe('https://example.com');
        expect(bookmark.title).toBe('Example');
      });

      it('ต้องเพิ่ม bookmark ลงใน local storage', () => {
        engine.createBookmark({
          url: 'https://example.com',
          title: 'Example'
        });

        const state = engine.getState();
        expect(state.bookmarks).toHaveLength(1);
      });

      // TEST: ต้องเพิ่ม test สำหรับ invalid URL
      it.skip('ต้องโยน error สำหรับ invalid URL', () => {
        expect(() => {
          engine.createBookmark({
            url: 'invalid-url',
            title: 'Example'
          });
        }).toThrow();
      });
    });

    describe('sync', () => {
      // MOCK: ยังใช้ mock data
      it('ต้องซิงค์ bookmarks ไปยัง server', async () => {
        // Mock server response
        const mockResponse = {
          success: true,
          serverVersion: 1
        };

        // Test implementation
      });
    });
  });
  ```

  ### 8.3 Test Patterns

  ```typescript
  // ใช้ AAA pattern (Arrange, Act, Assert)
  it('ต้องอัพเดท bookmark', () => {
    // Arrange - ตั้งค่า
    const bookmark = engine.createBookmark({
      url: 'https://example.com',
      title: 'Example'
    });

    // Act - ทำการ
    const updated = engine.updateBookmark(bookmark.id, {
      title: 'Updated'
    });

    // Assert - ตรวจสอบ
    expect(updated.title).toBe('Updated');
    expect(updated._version).toBe(1);
  });

  // ใช้ descriptive test names
  it('ต้องตรวจสอบ version เพื่อหา conflict', () => {
    // Implementation
  });

  // ใช้ beforeEach/afterEach สำหรับ setup/teardown
  describe('Database operations', () => {
    beforeEach(async () => {
      await db.connect();
    });

    afterEach(async () => {
      await db.disconnect();
    });

    it('ต้องสร้าง user', async () => {
      // Implementation
    });
  });
  ```

  ---

  ## 9. Git Workflow

  ### 9.1 Branch Naming

  ```
  ✅ GOOD:
  feature/add-bookmark-sync
  feature/implement-conflict-resolution
  bugfix/fix-race-condition
  docs/update-api-guide
  chore/update-dependencies

  ❌ BAD:
  feature
  fix-bug
  AddBookmarkSync
  update_api_guide
  ```

  ### 9.2 Commit Message Style

  ```
  ✅ GOOD:
  feat: เพิ่มการซิงค์ bookmarks ข้ามอุปกรณ์
  fix: แก้ race condition ในการซิงค์
  docs: อัพเดท API documentation
  test: เพิ่ม test สำหรับ conflict resolution
  chore: อัพเดท dependencies

  ❌ BAD:
  added sync feature
  fixed bug
  updated docs
  Added bookmark sync
  FIXED RACE CONDITION
  ```

  ### 9.3 Pull Request Template

  ```markdown
  ## Description
  ตรวจสอบว่าคำอธิบายชัดเจนและสมบูรณ์

  ## Type of Change
  - [ ] Bug fix
  - [ ] New feature
  - [ ] Documentation update

  ## Related Issues
  Closes #123

  ## Testing
  ตรวจสอบว่ามี test cases

  ## Checklist
  - [ ] Code follows style guide
  - [ ] Comments in Thai
  - [ ] Tests added/updated
  - [ ] Documentation updated
  - [ ] No breaking changes
  ```

  ---

  ## 10. Documentation Standards

  ### 10.1 Function Documentation

  ```typescript
  /**
   * ตรวจสอบและรวมการเปลี่ยนแปลงจากอุปกรณ์อื่น
   * 
   * @param localChanges - การเปลี่ยนแปลงจากอุปกรณ์ปัจจุบัน
   * @param remoteChanges - การเปลี่ยนแปลงจากอุปกรณ์อื่น
   * @returns การเปลี่ยนแปลงที่รวมแล้ว
   * @throws {ConflictError} ถ้าพบ conflict ที่ไม่สามารถแก้ได้
   * 
   * @example
   * const merged = await mergeChanges(local, remote);
   * 
   * DOC: ดูรายละเอียด algorithm ใน 003-ARCHITECTURE.md
   */
  async function mergeChanges(
    localChanges: SyncChange[],
    remoteChanges: SyncChange[]
  ): Promise<SyncChange[]> {
    // Implementation
  }
  ```

  ### 10.2 README Template

  ```markdown
  # Bookmark App - [Component Name]

  ตรวจสอบว่ามีคำอธิบายชัดเจน

  ## Overview
  ตรวจสอบว่ามีภาพรวม

  ## Installation
  ตรวจสอบว่ามีขั้นตอนการติดตั้ง

  ## Usage
  ตรวจสอบว่ามีตัวอย่างการใช้งาน

  ## API
  ตรวจสอบว่ามี API documentation

  ## Testing
  ตรวจสอบว่ามีขั้นตอนการ test

  ## Related Documents
  - DOC: 005-API-GUIDE.md
  - DOC: 003-ARCHITECTURE.md
  ```

  ---

  ## 11. Linting & Formatting

  ### 11.1 ESLint Configuration

  ```json
  {
    "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended"],
    "rules": {
      "no-console": "warn",
      "no-debugger": "error",
      "no-var": "error",
      "prefer-const": "error",
      "eqeqeq": "error",
      "@typescript-eslint/explicit-function-return-types": "error",
      "@typescript-eslint/no-explicit-any": "error",
      "@typescript-eslint/no-unused-vars": "error"
    }
  }
  ```

  ### 11.2 Prettier Configuration

  ```json
  {
    "semi": true,
    "trailingComma": "es5",
    "singleQuote": true,
    "printWidth": 80,
    "tabWidth": 2,
    "useTabs": false,
    "arrowParens": "always"
  }
  ```

  ---

  **Document End**

  *For questions or updates, please contact the development team.*
