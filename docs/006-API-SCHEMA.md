  # 📋 BOOKMARK APP - API SCHEMA & OPENAPI SPECIFICATION

  **Document Version:** 1.0  
  **Last Updated:** February 2026  
  **Status:** Active Development

  ---

  ## 📑 Table of Contents

  1. [OpenAPI Overview](#openapi-overview)
  2. [Authentication](#authentication)
  3. [API Endpoints](#api-endpoints)
  4. [Request/Response Schemas](#requestresponse-schemas)
  5. [Error Handling](#error-handling)
  6. [Rate Limiting](#rate-limiting)
  7. [Pagination](#pagination)
  8. [Versioning](#versioning)
  9. [OpenAPI Specification](#openapi-specification)
  10. [Tools & Resources](#tools--resources)

  ---

  ## 1. OpenAPI Overview

  ### 1.1 API Information

  ```yaml
  openapi: 3.0.0
  info:
    title: Bookmark App API
    description: |-
      Cross-platform bookmark manager API
      Local-first architecture with multi-device sync
    version: 1.0.0
    contact:
      name: API Support
      email: support@bookmark.bl1nk.site
      url: https://bookmark.bl1nk.site
    license:
      name: MIT
      url: https://opensource.org/licenses/MIT
  ```

  ### 1.2 Servers

  ```yaml
  servers:
    - url: http://localhost:3000
      description: Local development server
    - url: https://api-staging.bookmark.bl1nk.site
      description: Staging environment
    - url: https://api.bookmark.bl1nk.site
      description: Production environment
  ```

  ### 1.3 Tags

  ```yaml
  tags:
    - name: Authentication
      description: User authentication endpoints
    - name: Sync
      description: Bookmark synchronization endpoints
    - name: Devices
      description: Device management endpoints
    - name: User
      description: User profile endpoints
    - name: Health
      description: Health check endpoints
  ```

  ---

  ## 2. Authentication

  ### 2.1 Security Schemes

  ```yaml
  components:
    securitySchemes:
      bearerAuth:
        type: http
        scheme: bearer
        bearerFormat: JWT
        description: JWT access token (15 minutes expiration)
      refreshToken:
        type: apiKey
        in: cookie
        name: refreshToken
        description: Refresh token (7 days expiration)
  ```

  ### 2.2 JWT Token Structure

  **Access Token (15 minutes)**
  ```json
  {
    "iss": "bookmark-app",
    "sub": "user-id",
    "email": "user@example.com",
    "username": "username",
    "iat": 1676246400,
    "exp": 1676247300
  }
  ```

  **Refresh Token (7 days)**
  ```json
  {
    "iss": "bookmark-app",
    "sub": "user-id",
    "type": "refresh",
    "iat": 1676246400,
    "exp": 1676851200
  }
  ```

  ### 2.3 Authentication Flow

  ```
  1. User calls /api/auth/register or /api/auth/login
  2. Server returns accessToken and refreshToken
  3. Client stores tokens securely
  4. Client sends accessToken in Authorization header
  5. When accessToken expires, client calls /api/auth/refresh
  6. Server returns new accessToken
  7. Client continues with new token
  ```

  ---

  ## 3. API Endpoints

  ### 3.1 Authentication Endpoints

  **POST /api/auth/register**
  ```yaml
  operationId: registerUser
  summary: ลงทะเบียนผู้ใช้ใหม่
  tags: [Authentication]
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/RegisterRequest'
  responses:
    '201':
      description: ลงทะเบียนสำเร็จ
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/AuthResponse'
    '400':
      description: Invalid input
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
    '409':
      description: Email already registered
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
  ```

  **POST /api/auth/login**
  ```yaml
  operationId: loginUser
  summary: ล็อกอิน
  tags: [Authentication]
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/LoginRequest'
  responses:
    '200':
      description: ล็อกอินสำเร็จ
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/AuthResponse'
    '401':
      description: Invalid credentials
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ErrorResponse'
  ```

  **POST /api/auth/refresh**
  ```yaml
  operationId: refreshToken
  summary: รีเฟรช access token
  tags: [Authentication]
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/RefreshTokenRequest'
  responses:
    '200':
      description: Token refreshed successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/TokenResponse'
    '401':
      description: Invalid refresh token
  ```

  **GET /api/auth/me**
  ```yaml
  operationId: getCurrentUser
  summary: ดึงข้อมูลผู้ใช้ปัจจุบัน
  tags: [Authentication]
  security:
    - bearerAuth: []
  responses:
    '200':
      description: User data retrieved
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/UserResponse'
    '401':
      description: Unauthorized
  ```

  **POST /api/auth/logout**
  ```yaml
  operationId: logoutUser
  summary: ล็อกเอาต์
  tags: [Authentication]
  security:
    - bearerAuth: []
  responses:
    '200':
      description: Logged out successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/SuccessResponse'
    '401':
      description: Unauthorized
  ```

  ### 3.2 Sync Endpoints

  **POST /api/sync/push**
  ```yaml
  operationId: pushChanges
  summary: ส่งการเปลี่ยนแปลงไปยังเซิร์ฟเวอร์
  tags: [Sync]
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/PushRequest'
  responses:
    '200':
      description: Changes pushed successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/PushResponse'
    '409':
      description: Version conflict
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ConflictResponse'
  ```

  **POST /api/sync/pull**
  ```yaml
  operationId: pullChanges
  summary: ดึงการเปลี่ยนแปลงจากเซิร์ฟเวอร์
  tags: [Sync]
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/PullRequest'
  responses:
    '200':
      description: Changes retrieved successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/PullResponse'
  ```

  **GET /api/sync/status**
  ```yaml
  operationId: getSyncStatus
  summary: ดึงสถานะการซิงค์
  tags: [Sync]
  security:
    - bearerAuth: []
  responses:
    '200':
      description: Sync status retrieved
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/SyncStatusResponse'
  ```

  ### 3.3 Device Endpoints

  **POST /api/devices/register**
  ```yaml
  operationId: registerDevice
  summary: ลงทะเบียนอุปกรณ์ใหม่
  tags: [Devices]
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/RegisterDeviceRequest'
  responses:
    '201':
      description: Device registered successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/DeviceResponse'
  ```

  **GET /api/devices**
  ```yaml
  operationId: listDevices
  summary: ดึงรายชื่ออุปกรณ์ทั้งหมด
  tags: [Devices]
  security:
    - bearerAuth: []
  responses:
    '200':
      description: Devices retrieved
      content:
        application/json:
          schema:
            type: object
            properties:
              success:
                type: boolean
              data:
                type: array
                items:
                  $ref: '#/components/schemas/Device'
  ```

  **DELETE /api/devices/{id}**
  ```yaml
  operationId: deleteDevice
  summary: ลบอุปกรณ์
  tags: [Devices]
  security:
    - bearerAuth: []
  parameters:
    - name: id
      in: path
      required: true
      schema:
        type: string
  responses:
    '200':
      description: Device deleted successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/SuccessResponse'
    '404':
      description: Device not found
  ```

  ### 3.4 User Endpoints

  **GET /api/user/profile**
  ```yaml
  operationId: getUserProfile
  summary: ดึงโปรไฟล์ผู้ใช้
  tags: [User]
  security:
    - bearerAuth: []
  responses:
    '200':
      description: User profile retrieved
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/UserResponse'
  ```

  **PUT /api/user/profile**
  ```yaml
  operationId: updateUserProfile
  summary: อัพเดทโปรไฟล์ผู้ใช้
  tags: [User]
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/UpdateProfileRequest'
  responses:
    '200':
      description: Profile updated successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/UserResponse'
  ```

  **GET /api/user/settings**
  ```yaml
  operationId: getUserSettings
  summary: ดึงการตั้งค่าผู้ใช้
  tags: [User]
  security:
    - bearerAuth: []
  responses:
    '200':
      description: Settings retrieved
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/UserSettingsResponse'
  ```

  **PUT /api/user/settings**
  ```yaml
  operationId: updateUserSettings
  summary: อัพเดทการตั้งค่าผู้ใช้
  tags: [User]
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/UpdateSettingsRequest'
  responses:
    '200':
      description: Settings updated successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/UserSettingsResponse'
  ```

  **POST /api/user/export**
  ```yaml
  operationId: exportData
  summary: ส่งออกข้อมูลผู้ใช้
  tags: [User]
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/ExportRequest'
  responses:
    '200':
      description: Data exported successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ExportResponse'
  ```

  **POST /api/user/import**
  ```yaml
  operationId: importData
  summary: นำเข้าข้อมูลผู้ใช้
  tags: [User]
  security:
    - bearerAuth: []
  requestBody:
    required: true
    content:
      application/json:
        schema:
          $ref: '#/components/schemas/ImportRequest'
  responses:
    '200':
      description: Data imported successfully
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/ImportResponse'
  ```

  ### 3.5 Health Endpoint

  **GET /api/health**
  ```yaml
  operationId: healthCheck
  summary: ตรวจสอบสุขภาพของเซิร์ฟเวอร์
  tags: [Health]
  responses:
    '200':
      description: Server is healthy
      content:
        application/json:
          schema:
            $ref: '#/components/schemas/HealthResponse'
  ```

  ---

  ## 4. Request/Response Schemas

  ### 4.1 Authentication Schemas

  ```yaml
  components:
    schemas:
      RegisterRequest:
        type: object
        required: [email, password, username]
        properties:
          email:
            type: string
            format: email
            description: ที่อยู่อีเมล
          password:
            type: string
            format: password
            description: รหัสผ่าน (8+ chars, uppercase, number, special)
          username:
            type: string
            minLength: 3
            maxLength: 20
            description: ชื่อผู้ใช้

      LoginRequest:
        type: object
        required: [email, password]
        properties:
          email:
            type: string
            format: email
          password:
            type: string
            format: password

      RefreshTokenRequest:
        type: object
        required: [refreshToken]
        properties:
          refreshToken:
            type: string
            description: Refresh token

      AuthResponse:
        type: object
        properties:
          success:
            type: boolean
          data:
            type: object
            properties:
              userId:
                type: string
              email:
                type: string
              username:
                type: string
              displayName:
                type: string
              avatarUrl:
                type: string
              accessToken:
                type: string
              refreshToken:
                type: string
              expiresIn:
                type: integer
                description: Token expiration in seconds

      TokenResponse:
        type: object
        properties:
          success:
            type: boolean
          data:
            type: object
            properties:
              accessToken:
                type: string
              expiresIn:
                type: integer

      UserResponse:
        type: object
        properties:
          success:
            type: boolean
          data:
            $ref: '#/components/schemas/User'

      User:
        type: object
        properties:
          id:
            type: string
          email:
            type: string
          username:
            type: string
          displayName:
            type: string
          avatarUrl:
            type: string
          bio:
            type: string
          theme:
            type: string
            enum: [light, dark]
          language:
            type: string
          emailVerified:
            type: boolean
          createdAt:
            type: string
            format: date-time
          lastLoginAt:
            type: string
            format: date-time
  ```

  ### 4.2 Sync Schemas

  ```yaml
  components:
    schemas:
      PushRequest:
        type: object
        required: [deviceId, changes, clientVersion]
        properties:
          deviceId:
            type: string
            description: ID ของอุปกรณ์
          changes:
            type: array
            items:
              $ref: '#/components/schemas/SyncChange'
          clientVersion:
            type: integer
            description: เวอร์ชัน client ปัจจุบัน

      PushResponse:
        type: object
        properties:
          success:
            type: boolean
          serverVersion:
            type: integer
          conflicts:
            type: array
            items:
              $ref: '#/components/schemas/Conflict'
          timestamp:
            type: integer

      PullRequest:
        type: object
        required: [deviceId, clientVersion]
        properties:
          deviceId:
            type: string
          clientVersion:
            type: integer
          lastSyncedAt:
            type: string
            format: date-time

      PullResponse:
        type: object
        properties:
          success:
            type: boolean
          changes:
            type: array
            items:
              $ref: '#/components/schemas/SyncChange'
          serverVersion:
            type: integer
          timestamp:
            type: integer

      SyncChange:
        type: object
        required: [id, type, action, data]
        properties:
          id:
            type: string
          type:
            type: string
            enum: [bookmark, tag, note]
          action:
            type: string
            enum: [create, update, delete]
          data:
            type: object
          timestamp:
            type: integer
          _version:
            type: integer
          deviceId:
            type: string

      Conflict:
        type: object
        properties:
          id:
            type: string
          type:
            type: string
          localVersion:
            type: object
          remoteVersion:
            type: object
          resolution:
            type: string
            enum: [local, remote, manual]

      SyncStatusResponse:
        type: object
        properties:
          success:
            type: boolean
          data:
            type: object
            properties:
              currentVersion:
                type: integer
              lastSyncedAt:
                type: string
                format: date-time
              devices:
                type: array
                items:
                  $ref: '#/components/schemas/Device'
  ```

  ### 4.3 Device Schemas

  ```yaml
  components:
    schemas:
      RegisterDeviceRequest:
        type: object
        required: [name, type, platform]
        properties:
          name:
            type: string
            description: ชื่ออุปกรณ์
          type:
            type: string
            enum: [desktop, mobile, web, extension]
          platform:
            type: string
            enum: [macos, windows, linux, ios, android]
          deviceFingerprint:
            type: string

      DeviceResponse:
        type: object
        properties:
          success:
            type: boolean
          data:
            $ref: '#/components/schemas/Device'

      Device:
        type: object
        properties:
          id:
            type: string
          userId:
            type: string
          name:
            type: string
          type:
            type: string
          platform:
            type: string
          lastSyncedAt:
            type: string
            format: date-time
          lastSyncVersion:
            type: integer
          createdAt:
            type: string
            format: date-time
          updatedAt:
            type: string
            format: date-time
  ```

  ### 4.4 User Settings Schemas

  ```yaml
  components:
    schemas:
      UserSettingsResponse:
        type: object
        properties:
          success:
            type: boolean
          data:
            $ref: '#/components/schemas/UserSettings'

      UserSettings:
        type: object
        properties:
          id:
            type: string
          userId:
            type: string
          autoSync:
            type: boolean
          syncInterval:
            type: integer
          notifyOnSync:
            type: boolean
          notifyOnConflict:
            type: boolean
          isPublicProfile:
            type: boolean
          allowDataExport:
            type: boolean
          maxStorageGB:
            type: integer
          customSettings:
            type: object

      UpdateSettingsRequest:
        type: object
        properties:
          autoSync:
            type: boolean
          syncInterval:
            type: integer
          notifyOnSync:
            type: boolean
          notifyOnConflict:
            type: boolean
          isPublicProfile:
            type: boolean
  ```

  ### 4.5 Export/Import Schemas

  ```yaml
  components:
    schemas:
      ExportRequest:
        type: object
        required: [format]
        properties:
          format:
            type: string
            enum: [json, csv, html]

      ExportResponse:
        type: object
        properties:
          success:
            type: boolean
          data:
            type: object
            properties:
              downloadUrl:
                type: string
              filename:
                type: string
              format:
                type: string
              size:
                type: integer

      ImportRequest:
        type: object
        required: [file, format]
        properties:
          file:
            type: string
            format: binary
          format:
            type: string
            enum: [json, csv, html]

      ImportResponse:
        type: object
        properties:
          success:
            type: boolean
          data:
            type: object
            properties:
              imported:
                type: integer
              duplicates:
                type: integer
              errors:
                type: integer
  ```

  ---

  ## 5. Error Handling

  ### 5.1 Error Response Schema

  ```yaml
  components:
    schemas:
      ErrorResponse:
        type: object
        properties:
          error:
            type: string
            description: ข้อความข้อผิดพลาด
          code:
            type: string
            description: รหัสข้อผิดพลาด
          details:
            type: array
            items:
              type: object
              properties:
                field:
                  type: string
                message:
                  type: string

      ConflictResponse:
        type: object
        properties:
          error:
            type: string
          code:
            type: string
          conflicts:
            type: array
            items:
              $ref: '#/components/schemas/Conflict'
  ```

  ### 5.2 HTTP Status Codes

  ```
  200 OK              - Request successful
  201 Created         - Resource created
  204 No Content      - Request successful, no content
  400 Bad Request     - Invalid input
  401 Unauthorized    - Missing or invalid token
  403 Forbidden       - No permission
  404 Not Found       - Resource not found
  409 Conflict        - Version conflict or duplicate
  429 Too Many        - Rate limit exceeded
  500 Server Error    - Internal server error
  503 Unavailable     - Service unavailable
  ```

  ---

  ## 6. Rate Limiting

  ### 6.1 Rate Limit Headers

  ```
  X-RateLimit-Limit:       100
  X-RateLimit-Remaining:   95
  X-RateLimit-Reset:       1676246500
  Retry-After:             60
  ```

  ### 6.2 Rate Limit Rules

  ```
  /api/auth/login:         5 requests per 15 minutes per IP
  /api/auth/register:      3 requests per 1 hour per IP
  /api/sync/push:          100 requests per 1 minute per user
  /api/sync/pull:          100 requests per 1 minute per user
  /api/user/export:        5 requests per 1 hour per user
  /api/user/import:        5 requests per 1 hour per user
  Other endpoints:         1000 requests per 1 hour per user
  ```

  ---

  ## 7. Pagination

  ### 7.1 Pagination Query Parameters

  ```
  ?page=1              - หน้าที่ 1 (default)
  ?limit=20            - 20 items per page (default)
  ?sort=createdAt      - เรียงตามฟิลด์
  ?order=desc          - ลำดับ (asc/desc)
  ```

  ### 7.2 Pagination Response

  ```json
  {
    "success": true,
    "data": [...],
    "pagination": {
      "page": 1,
      "limit": 20,
      "total": 100,
      "pages": 5,
      "hasNext": true,
      "hasPrev": false
    }
  }
  ```

  ---

  ## 8. Versioning

  ### 8.1 API Versioning

  ```
  Current Version:  v1
  Base URL:         /api/v1/
  
  Future:           /api/v2/
  ```

  ### 8.2 Deprecation Policy

  ```
  - Announce deprecation 6 months in advance
  - Support old version for 1 year
  - Provide migration guide
  - Send notifications to API users
  ```

  ---

  ## 9. OpenAPI Specification

  ### 9.1 Complete OpenAPI File

  ดูไฟล์ `api/openapi.json` สำหรับ specification ที่สมบูรณ์

  ### 9.2 Generate OpenAPI Spec

  ```bash
  # ติดตั้ง swagger-jsdoc
  pnpm add -D swagger-jsdoc

  # สร้าง openapi.json
  pnpm generate-openapi

  # ดู Swagger UI
  http://localhost:3000/api/docs
  ```

  ### 9.3 OpenAPI Tools

  ```
  Swagger UI:       http://localhost:3000/api/docs
  ReDoc:            http://localhost:3000/api/redoc
  OpenAPI JSON:     http://localhost:3000/api/openapi.json
  OpenAPI YAML:     http://localhost:3000/api/openapi.yaml
  ```

  ---

  ## 10. Tools & Resources

  ### 10.1 API Testing Tools

  ```
  Postman:          https://www.postman.com/
  Insomnia:         https://insomnia.rest/
  Thunder Client:   https://www.thunderclient.com/
  REST Client:      https://marketplace.visualstudio.com/items?itemName=humao.rest-client
  ```

  ### 10.2 API Documentation

  ```
  Swagger UI:       http://localhost:3000/api/docs
  ReDoc:            http://localhost:3000/api/redoc
  OpenAPI Spec:     http://localhost:3000/api/openapi.json
  ```

  ### 10.3 API Client Libraries

  ```
  JavaScript:       axios, fetch, node-fetch
  Python:           requests, httpx
  Go:               net/http, resty
  Java:             okhttp, retrofit
  Ruby:             httparty, faraday
  ```

  ---

  **Document End**

  *For questions or updates, please contact the API team.*
