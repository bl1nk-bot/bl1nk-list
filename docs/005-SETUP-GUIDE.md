  # 🚀 BOOKMARK APP - SETUP & INSTALLATION GUIDE

  **Document Version:** 1.0  
  **Last Updated:** February 2026  
  **Status:** Active Development

  ---

  ## 📑 Table of Contents

  1. [Prerequisites](#prerequisites)
  2. [Project Setup](#project-setup)
  3. [Backend Setup](#backend-setup)
  4. [Frontend Setup](#frontend-setup)
  5. [Desktop Setup](#desktop-setup)
  6. [Mobile Setup](#mobile-setup)
  7. [Environment Configuration](#environment-configuration)
  8. [Database Setup](#database-setup)
  9. [Running Locally](#running-locally)
  10. [Troubleshooting](#troubleshooting)

  ---

  ## 1. Prerequisites

  ### 1.1 System Requirements

  **macOS / Linux / Windows**
  ```
  ✅ Node.js 18+ (LTS)
  ✅ pnpm 8.0+
  ✅ Git 2.30+
  ✅ Docker (optional, for local database)
  ✅ Rust (สำหรับ Desktop app)
  ✅ Flutter 3.13+ (สำหรับ Mobile app)
  ```

  ### 1.2 Installation

  **Node.js**
  ```bash
  # ตรวจสอบ version
  node --version  # ต้องเป็น v18.0.0 ขึ้นไป

  # ติดตั้งจาก https://nodejs.org/
  ```

  **pnpm**
  ```bash
  # ติดตั้ง pnpm
  npm install -g pnpm@latest

  # ตรวจสอบ version
  pnpm --version  # ต้องเป็น 8.0.0 ขึ้นไป
  ```

  **Git**
  ```bash
  # ติดตั้งจาก https://git-scm.com/
  git --version
  ```

  **Rust (สำหรับ Desktop)**
  ```bash
  # ติดตั้ง Rust
  curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

  # ตรวจสอบ version
  rustc --version
  cargo --version
  ```

  **Flutter (สำหรับ Mobile)**
  ```bash
  # ติดตั้ง Flutter
  # ดาวน์โหลดจาก https://flutter.dev/docs/get-started/install

  # ตรวจสอบ version
  flutter --version

  # ตรวจสอบ dependencies
  flutter doctor
  ```

  ---

  ## 2. Project Setup

  ### 2.1 Clone Repository

  ```bash
  # Clone project
  git clone https://github.com/bl1nk/bookmark-app.git
  cd bookmark-app

  # ตรวจสอบ branch
  git branch -a
  ```

  ### 2.2 Install Dependencies

  ```bash
  # ติดตั้ง dependencies ทั้งหมด
  pnpm install

  # ตรวจสอบ workspace
  pnpm list

  # ติดตั้ง dependencies สำหรับ package เฉพาะ
  pnpm install --filter @bookmark/web
  pnpm install --filter @bookmark/api
  ```

  ### 2.3 Project Structure

  ```
  bookmark-app/
  ├─ packages/
  │  ├─ shared/          # Shared libraries
  │  ├─ web/             # Web app (React)
  │  ├─ desktop/         # Desktop app (Tauri)
  │  └─ mobile/          # Mobile app (Flutter)
  ├─ api/                # Backend API
  ├─ docs/               # Documentation
  ├─ .github/            # GitHub workflows
  ├─ pnpm-workspace.yaml # Workspace config
  ├─ package.json        # Root package.json
  └─ README.md           # Project README
  ```

  ---

  ## 3. Backend Setup

  ### 3.1 Environment Configuration

  ```bash
  # ไปที่ api directory
  cd api

  # สร้าง .env file
  cp .env.example .env

  # แก้ไข .env ด้วย editor
  # ดูรายละเอียดใน section 7
  ```

  ### 3.2 Database Setup

  **Option 1: ใช้ NeonDB (Production)**
  ```bash
  # สร้าง account ที่ https://neon.tech/
  # สร้าง project และ database
  # คัดลอก connection string ไปยัง DATABASE_URL ใน .env

  DATABASE_URL="postgresql://user:password@host/database"
  ```

  **Option 2: ใช้ Local PostgreSQL (Development)**
  ```bash
  # ติดตั้ง PostgreSQL
  # macOS
  brew install postgresql@15

  # Linux
  sudo apt-get install postgresql postgresql-contrib

  # Windows
  # ดาวน์โหลดจาก https://www.postgresql.org/download/windows/

  # เริ่ม PostgreSQL
  brew services start postgresql@15

  # สร้าง database
  createdb bookmark_dev

  # ตั้ง DATABASE_URL
  DATABASE_URL="postgresql://localhost/bookmark_dev"
  ```

  **Option 3: ใช้ Docker**
  ```bash
  # สร้าง docker-compose.yml
  cat > docker-compose.yml << 'EOF'
  version: '3.8'
  services:
    postgres:
      image: postgres:15-alpine
      environment:
        POSTGRES_DB: bookmark_dev
        POSTGRES_PASSWORD: password
      ports:
        - "5432:5432"
      volumes:
        - postgres_data:/var/lib/postgresql/data

    redis:
      image: redis:7-alpine
      ports:
        - "6379:6379"

  volumes:
    postgres_data:
  EOF

  # เริ่ม services
  docker-compose up -d

  # ตั้ง DATABASE_URL
  DATABASE_URL="postgresql://postgres:password@localhost/bookmark_dev"
  ```

  ### 3.3 Prisma Setup

  ```bash
  # ไปที่ api directory
  cd api

  # สร้าง migration
  pnpm prisma migrate dev --name init

  # ตรวจสอบ schema
  pnpm prisma studio

  # Generate Prisma client
  pnpm prisma generate
  ```

  ### 3.4 Install API Dependencies

  ```bash
  # ติดตั้ง dependencies
  pnpm install

  # ตรวจสอบ dependencies
  pnpm list
  ```

  ---

  ## 4. Frontend Setup

  ### 4.1 Web App Setup

  ```bash
  # ไปที่ web directory
  cd packages/web

  # ติดตั้ง dependencies
  pnpm install

  # สร้าง .env file
  cp .env.example .env

  # แก้ไข .env
  VITE_API_URL=http://localhost:3000
  VITE_APP_NAME=Bookmark App
  ```

  ### 4.2 Verify Installation

  ```bash
  # ตรวจสอบ dependencies
  pnpm list

  # ตรวจสอบ TypeScript
  pnpm tsc --noEmit

  # ตรวจสอบ ESLint
  pnpm lint
  ```

  ---

  ## 5. Desktop Setup

  ### 5.1 Tauri Setup

  ```bash
  # ไปที่ desktop directory
  cd packages/desktop

  # ติดตั้ง dependencies
  pnpm install

  # ติดตั้ง Tauri CLI
  pnpm add -D @tauri-apps/cli

  # สร้าง .env file
  cp .env.example .env
  ```

  ### 5.2 Tauri Configuration

  ```bash
  # ตรวจสอบ Tauri setup
  pnpm tauri info

  # Build Tauri app (development)
  pnpm tauri dev

  # Build Tauri app (production)
  pnpm tauri build
  ```

  ### 5.3 Troubleshooting Tauri

  ```bash
  # ถ้า Rust compilation ล้มเหลว
  rustup update

  # ถ้า WebView ไม่ทำงาน
  # macOS: ต้องมี Xcode Command Line Tools
  xcode-select --install

  # Windows: ต้องมี Visual Studio Build Tools
  # ดาวน์โหลดจาก https://visualstudio.microsoft.com/downloads/
  ```

  ---

  ## 6. Mobile Setup

  ### 6.1 Flutter Setup

  ```bash
  # ไปที่ mobile directory
  cd packages/mobile

  # ตรวจสอบ Flutter setup
  flutter doctor

  # ติดตั้ง dependencies
  flutter pub get

  # สร้าง .env file
  cp .env.example .env
  ```

  ### 6.2 iOS Setup (macOS only)

  ```bash
  # ไปที่ iOS directory
  cd ios

  # ติดตั้ง pods
  pod install

  # กลับไป
  cd ..

  # รัน app บน iOS simulator
  flutter run -d macos
  ```

  ### 6.3 Android Setup

  ```bash
  # ตรวจสอบ Android setup
  flutter doctor -v

  # รัน app บน Android emulator
  flutter run
  ```

  ### 6.4 Build APK/IPA

  ```bash
  # Build APK (Android)
  flutter build apk --release

  # Build IPA (iOS)
  flutter build ios --release
  ```

  ---

  ## 7. Environment Configuration

  ### 7.1 Backend Environment (.env)

  ```bash
  # ไฟล์: api/.env

  # Database
  DATABASE_URL="postgresql://user:password@localhost/bookmark_dev"

  # Redis
  REDIS_URL="redis://localhost:6379"
  UPSTASH_REDIS_URL="https://..."
  UPSTASH_REDIS_TOKEN="..."

  # JWT Secrets (ต้องเปลี่ยนใน production)
  JWT_SECRET="your-super-secret-key-change-in-production"
  JWT_REFRESH_SECRET="your-super-refresh-secret-key-change-in-production"

  # OAuth (GitHub)
  GITHUB_CLIENT_ID="your-github-client-id"
  GITHUB_CLIENT_SECRET="your-github-client-secret"

  # OAuth (Google)
  GOOGLE_CLIENT_ID="your-google-client-id"
  GOOGLE_CLIENT_SECRET="your-google-client-secret"

  # API Configuration
  API_PORT=3000
  API_HOST="localhost"
  NODE_ENV="development"

  # CORS
  CORS_ORIGIN="http://localhost:5173,http://localhost:3000"

  # Monitoring
  SENTRY_DSN="https://..."

  # Email (ถ้าต้องใช้)
  SMTP_HOST="smtp.gmail.com"
  SMTP_PORT=587
  SMTP_USER="your-email@gmail.com"
  SMTP_PASSWORD="your-app-password"
  ```

  ### 7.2 Frontend Environment (.env)

  ```bash
  # ไฟล์: packages/web/.env

  # API Configuration
  VITE_API_URL="http://localhost:3000"
  VITE_API_TIMEOUT=10000

  # App Configuration
  VITE_APP_NAME="Bookmark App"
  VITE_APP_VERSION="1.0.0"

  # Feature Flags
  VITE_ENABLE_ANALYTICS=true
  VITE_ENABLE_ERROR_TRACKING=true

  # OAuth (ถ้าต้องใช้)
  VITE_GITHUB_CLIENT_ID="your-github-client-id"
  VITE_GOOGLE_CLIENT_ID="your-google-client-id"
  ```

  ### 7.3 Desktop Environment (.env)

  ```bash
  # ไฟล์: packages/desktop/.env

  # API Configuration
  VITE_API_URL="http://localhost:3000"

  # App Configuration
  VITE_APP_NAME="Bookmark App"
  VITE_APP_VERSION="1.0.0"

  # Tauri Configuration
  TAURI_PRIVATE_KEY="your-private-key"
  TAURI_KEY_PASSWORD="your-key-password"
  ```

  ### 7.4 Mobile Environment (.env)

  ```bash
  # ไฟล์: packages/mobile/.env

  # API Configuration
  API_URL="http://localhost:3000"

  # App Configuration
  APP_NAME="Bookmark App"
  APP_VERSION="1.0.0"
  ```

  ---

  ## 8. Database Setup

  ### 8.1 Prisma Migrations

  ```bash
  # ไปที่ api directory
  cd api

  # สร้าง migration ใหม่
  pnpm prisma migrate dev --name add_new_field

  # ดูประวัติ migrations
  pnpm prisma migrate status

  # Rollback migration
  pnpm prisma migrate resolve --rolled-back migration_name

  # Reset database (development only)
  pnpm prisma migrate reset
  ```

  ### 8.2 Seed Database

  ```bash
  # สร้าง seed script
  cat > prisma/seed.ts << 'EOF'
  import { prisma } from '../src/db';

  async function main() {
    // TODO: เพิ่ม seed data
    console.log('Database seeded');
  }

  main()
    .catch((e) => {
      console.error(e);
      process.exit(1);
    })
    .finally(async () => {
      await prisma.$disconnect();
    });
  EOF

  # รัน seed
  pnpm prisma db seed
  ```

  ### 8.3 Database Backup

  ```bash
  # Backup database
  pg_dump bookmark_dev > backup.sql

  # Restore database
  psql bookmark_dev < backup.sql
  ```

  ---

  ## 9. Running Locally

  ### 9.1 Terminal Setup

  ```bash
  # เปิด 3 terminals

  # Terminal 1: Backend API
  cd api
  pnpm dev

  # Terminal 2: Frontend Web
  cd packages/web
  pnpm dev

  # Terminal 3: Desktop App (optional)
  cd packages/desktop
  pnpm tauri dev
  ```

  ### 9.2 Access Applications

  ```
  Web App:      http://localhost:5173
  API Server:   http://localhost:3000
  API Docs:     http://localhost:3000/api/docs
  Prisma Studio: http://localhost:5555
  ```

  ### 9.3 Development Commands

  ```bash
  # ทั้งหมด
  pnpm dev                    # รัน dev servers ทั้งหมด

  # Backend
  cd api
  pnpm dev                    # รัน API server
  pnpm test                   # รัน tests
  pnpm lint                   # ตรวจสอบ code style
  pnpm format                 # format code

  # Frontend
  cd packages/web
  pnpm dev                    # รัน dev server
  pnpm build                  # build production
  pnpm preview                # preview production build
  pnpm test                   # รัน tests
  pnpm lint                   # ตรวจสอบ code style

  # Desktop
  cd packages/desktop
  pnpm tauri dev              # รัน desktop app
  pnpm tauri build            # build production

  # Mobile
  cd packages/mobile
  flutter run                 # รัน mobile app
  flutter build apk           # build APK
  ```

  ---

  ## 10. Troubleshooting

  ### 10.1 Common Issues

  **Issue: pnpm install ล้มเหลว**
  ```bash
  # Solution 1: ลบ node_modules และ lock file
  rm -rf node_modules pnpm-lock.yaml
  pnpm install

  # Solution 2: ใช้ pnpm store prune
  pnpm store prune
  pnpm install

  # Solution 3: ตรวจสอบ Node version
  node --version  # ต้องเป็น v18+
  ```

  **Issue: Database connection ล้มเหลว**
  ```bash
  # ตรวจสอบ PostgreSQL
  psql -U postgres -d bookmark_dev

  # ตรวจสอบ DATABASE_URL ใน .env
  echo $DATABASE_URL

  # ตรวจสอบ Prisma connection
  cd api
  pnpm prisma db push
  ```

  **Issue: Port already in use**
  ```bash
  # หา process ที่ใช้ port
  lsof -i :3000      # Backend
  lsof -i :5173      # Frontend

  # Kill process
  kill -9 <PID>

  # หรือเปลี่ยน port ใน .env
  API_PORT=3001
  VITE_API_URL=http://localhost:3001
  ```

  **Issue: TypeScript errors**
  ```bash
  # ตรวจสอบ TypeScript
  pnpm tsc --noEmit

  # Generate types
  pnpm prisma generate

  # Rebuild
  pnpm build
  ```

  **Issue: Tauri build ล้มเหลว**
  ```bash
  # ตรวจสอบ Rust
  rustup update
  rustc --version

  # ลบ target directory
  rm -rf src-tauri/target

  # Rebuild
  pnpm tauri build
  ```

  ### 10.2 Debug Mode

  ```bash
  # Enable debug logging
  DEBUG=* pnpm dev

  # Backend debug
  cd api
  NODE_DEBUG=* pnpm dev

  # Frontend debug
  cd packages/web
  DEBUG=* pnpm dev
  ```

  ### 10.3 Getting Help

  ```
  📖 Documentation:  docs/
  🐛 Issues:        https://github.com/bl1nk/bookmark-app/issues
  💬 Discussions:   https://github.com/bl1nk/bookmark-app/discussions
  📧 Email:         support@bookmark.bl1nk.site
  ```

  ---

  **Document End**

  *For questions or updates, please contact the development team.*
