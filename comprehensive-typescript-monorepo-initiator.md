# Comprehensive TypeScript Monorepo Project Initiator

A complete guide to setting up a production-ready TypeScript monorepo with modern tooling, framework-agnostic architecture, and runtime flexibility.

## Table of Contents

1. [Project Structure](#project-structure)
2. [Initial Setup](#initial-setup)
3. [Package Manager Configuration](#package-manager-configuration)
4. [TypeScript Configuration](#typescript-configuration)
5. [Code Quality Tools](#code-quality-tools)
6. [Git Hooks with Husky](#git-hooks-with-husky)
7. [Shared Packages](#shared-packages)
8. [Database Setup](#database-setup)
9. [Scripts and Automation](#scripts-and-automation)
10. [Environment Configuration](#environment-configuration)

---

## Project Structure

```
monorepo/
├── .husky/                      # Git hooks
├── apps/                        # Application packages
│   ├── web/                     # Frontend app (React/Vue/Svelte/etc)
│   └── api/                     # Backend app (Express/Fastify/Hono/etc)
├── packages/                    # Shared packages
│   ├── types/                   # Shared TypeScript types
│   ├── utils/                   # Shared utilities
│   ├── config/                  # Shared configurations
│   ├── database/                # Database client and migrations
│   └── ui/                      # Shared UI components (optional)
├── configs/                     # Root-level configurations
│   ├── typescript/              # TypeScript configs
│   ├── eslint/                  # ESLint configs
│   └── prettier/                # Prettier configs
├── scripts/                     # Build and automation scripts
├── .gitignore
├── .prettierrc.js
├── .prettierignore
├── package.json
├── pnpm-workspace.yaml          # or npm/yarn workspaces
├── tsconfig.json
└── turbo.json                   # Optional: Turborepo config
```

---

## Initial Setup

### 1. Initialize the Project

```bash
# Create project directory
mkdir my-monorepo && cd my-monorepo

# Initialize git
git init

# Initialize package.json
npm init -y
# or
pnpm init
# or
bun init
```

### 2. Configure Workspace

**For pnpm** (recommended):
```yaml
# pnpm-workspace.yaml
packages:
  - 'apps/*'
  - 'packages/*'
```

**For npm/yarn**:
```json
// package.json
{
  "workspaces": [
    "apps/*",
    "packages/*"
  ]
}
```

### 3. Root package.json

```json
{
  "name": "my-monorepo",
  "version": "1.0.0",
  "private": true,
  "scripts": {
    "dev": "turbo run dev",
    "build": "turbo run build",
    "test": "turbo run test",
    "lint": "turbo run lint",
    "format": "prettier --write \"**/*.{ts,tsx,js,jsx,json,md}\"",
    "format:check": "prettier --check \"**/*.{ts,tsx,js,jsx,json,md}\"",
    "type-check": "turbo run type-check",
    "clean": "turbo run clean && rm -rf node_modules",
    "prepare": "husky"
  },
  "devDependencies": {
    "@types/node": "^20.0.0",
    "husky": "^9.0.0",
    "lint-staged": "^15.0.0",
    "prettier": "^3.0.0",
    "turbo": "^1.13.0",
    "typescript": "^5.4.0"
  },
  "engines": {
    "node": ">=18.0.0",
    "pnpm": ">=8.0.0"
  },
  "packageManager": "pnpm@8.15.0"
}
```

---

## Package Manager Configuration

### pnpm Configuration

```ini
# .npmrc
auto-install-peers=true
strict-peer-dependencies=false
shamefully-hoist=false
```

### Bun Configuration

```toml
# bunfig.toml
[install]
peer = true
production = false

[install.cache]
dir = ".bun-cache"
```

---

## TypeScript Configuration

### Base Configuration

```json
// configs/typescript/base.json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "lib": ["ES2022"],
    "moduleResolution": "bundler",
    "resolveJsonModule": true,
    "allowJs": true,
    "checkJs": false,
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedIndexedAccess": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "composite": true,
    "incremental": true
  },
  "exclude": ["node_modules", "dist", "build"]
}
```

### Node.js Configuration

```json
// configs/typescript/node.json
{
  "extends": "./base.json",
  "compilerOptions": {
    "module": "NodeNext",
    "moduleResolution": "NodeNext",
    "lib": ["ES2022"],
    "outDir": "dist",
    "rootDir": "src"
  }
}
```

### React Configuration

```json
// configs/typescript/react.json
{
  "extends": "./base.json",
  "compilerOptions": {
    "jsx": "react-jsx",
    "lib": ["ES2022", "DOM", "DOM.Iterable"]
  }
}
```

### Root tsconfig.json

```json
// tsconfig.json
{
  "files": [],
  "references": [
    { "path": "./apps/web" },
    { "path": "./apps/api" },
    { "path": "./packages/types" },
    { "path": "./packages/utils" },
    { "path": "./packages/database" }
  ]
}
```

---

## Code Quality Tools

### Prettier Configuration

```javascript
// .prettierrc.js
module.exports = {
  semi: true,
  trailingComma: 'es5',
  singleQuote: true,
  printWidth: 100,
  tabWidth: 2,
  useTabs: false,
  arrowParens: 'always',
  endOfLine: 'lf',
  plugins: [],
};
```

```
# .prettierignore
node_modules
dist
build
coverage
.next
.turbo
*.log
pnpm-lock.yaml
package-lock.json
yarn.lock
```

### ESLint Configuration (Optional)

```javascript
// configs/eslint/base.js
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'prettier',
  ],
  parser: '@typescript-eslint/parser',
  plugins: ['@typescript-eslint'],
  root: true,
  env: {
    node: true,
    es2022: true,
  },
  ignorePatterns: ['dist', 'node_modules', 'build'],
};
```

---

## Git Hooks with Husky

### Setup Husky

```bash
# Install husky
pnpm add -D husky lint-staged

# Initialize husky
pnpm exec husky init
```

### Pre-commit Hook

```bash
# .husky/pre-commit
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

pnpm lint-staged
```

### Pre-push Hook

```bash
# .husky/pre-push
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

pnpm type-check
pnpm test
```

### Commit Message Hook

```bash
# .husky/commit-msg
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

pnpm commitlint --edit $1
```

### Lint-staged Configuration

```json
// package.json
{
  "lint-staged": {
    "*.{ts,tsx,js,jsx}": [
      "prettier --write",
      "eslint --fix"
    ],
    "*.{json,md,yml,yaml}": [
      "prettier --write"
    ]
  }
}
```

---

## Shared Packages

### 1. Shared Types Package

```json
// packages/types/package.json
{
  "name": "@monorepo/types",
  "version": "1.0.0",
  "main": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "default": "./dist/index.js"
    }
  },
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch",
    "clean": "rm -rf dist"
  },
  "devDependencies": {
    "typescript": "^5.4.0"
  }
}
```

```typescript
// packages/types/src/index.ts
export * from './user';
export * from './api';
export * from './database';
```

```typescript
// packages/types/src/user.ts
export interface User {
  id: string;
  email: string;
  name: string;
  createdAt: Date;
  updatedAt: Date;
}

export type CreateUserInput = Omit<User, 'id' | 'createdAt' | 'updatedAt'>;
export type UpdateUserInput = Partial<CreateUserInput>;
```

```json
// packages/types/tsconfig.json
{
  "extends": "../../configs/typescript/base.json",
  "compilerOptions": {
    "outDir": "dist",
    "rootDir": "src"
  },
  "include": ["src"]
}
```

### 2. Shared Utils Package

```json
// packages/utils/package.json
{
  "name": "@monorepo/utils",
  "version": "1.0.0",
  "main": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "exports": {
    ".": {
      "types": "./dist/index.d.ts",
      "default": "./dist/index.js"
    }
  },
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch",
    "test": "vitest",
    "clean": "rm -rf dist"
  },
  "dependencies": {
    "@monorepo/types": "workspace:*"
  },
  "devDependencies": {
    "typescript": "^5.4.0",
    "vitest": "^1.0.0"
  }
}
```

```typescript
// packages/utils/src/index.ts
export * from './validation';
export * from './formatting';
export * from './date';
```

### 3. Shared Config Package

```json
// packages/config/package.json
{
  "name": "@monorepo/config",
  "version": "1.0.0",
  "main": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch",
    "clean": "rm -rf dist"
  },
  "dependencies": {
    "dotenv": "^16.0.0",
    "zod": "^3.22.0"
  },
  "devDependencies": {
    "typescript": "^5.4.0"
  }
}
```

```typescript
// packages/config/src/index.ts
import { z } from 'zod';
import dotenv from 'dotenv';

dotenv.config();

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
  PORT: z.string().default('3000'),
  DATABASE_URL: z.string(),
  JWT_SECRET: z.string(),
});

export const env = envSchema.parse(process.env);

export const config = {
  env: env.NODE_ENV,
  port: parseInt(env.PORT, 10),
  database: {
    url: env.DATABASE_URL,
  },
  jwt: {
    secret: env.JWT_SECRET,
  },
} as const;
```

---

## Database Setup

### Database Package Structure

```json
// packages/database/package.json
{
  "name": "@monorepo/database",
  "version": "1.0.0",
  "main": "./dist/index.js",
  "types": "./dist/index.d.ts",
  "scripts": {
    "build": "tsc",
    "dev": "tsc --watch",
    "migrate": "drizzle-kit generate",
    "migrate:run": "tsx src/migrate.ts",
    "studio": "drizzle-kit studio",
    "clean": "rm -rf dist"
  },
  "dependencies": {
    "@monorepo/config": "workspace:*",
    "@monorepo/types": "workspace:*",
    "drizzle-orm": "^0.30.0",
    "postgres": "^3.4.0"
  },
  "devDependencies": {
    "drizzle-kit": "^0.20.0",
    "tsx": "^4.7.0",
    "typescript": "^5.4.0"
  }
}
```

```typescript
// packages/database/src/index.ts
export * from './client';
export * from './schema';
export * from './queries';
```

```typescript
// packages/database/src/client.ts
import { drizzle } from 'drizzle-orm/postgres-js';
import postgres from 'postgres';
import { config } from '@monorepo/config';

const client = postgres(config.database.url);
export const db = drizzle(client);
```

```typescript
// packages/database/src/schema/users.ts
import { pgTable, uuid, varchar, timestamp } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: varchar('email', { length: 255 }).notNull().unique(),
  name: varchar('name', { length: 255 }).notNull(),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull(),
});
```

```typescript
// packages/database/drizzle.config.ts
import type { Config } from 'drizzle-kit';
import { config } from '@monorepo/config';

export default {
  schema: './src/schema/index.ts',
  out: './migrations',
  driver: 'pg',
  dbCredentials: {
    connectionString: config.database.url,
  },
} satisfies Config;
```

---

## Scripts and Automation

### Turborepo Configuration

```json
// turbo.json
{
  "$schema": "https://turbo.build/schema.json",
  "globalDependencies": ["**/.env.*local"],
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**", "build/**"]
    },
    "dev": {
      "cache": false,
      "persistent": true
    },
    "test": {
      "dependsOn": ["build"],
      "outputs": ["coverage/**"]
    },
    "lint": {
      "dependsOn": ["^build"]
    },
    "type-check": {
      "dependsOn": ["^build"]
    },
    "clean": {
      "cache": false
    }
  }
}
```

### Build Scripts

```bash
# scripts/build.sh
#!/bin/bash

echo "Building all packages..."
pnpm turbo run build

echo "Build complete!"
```

```bash
# scripts/clean.sh
#!/bin/bash

echo "Cleaning all build artifacts..."
pnpm turbo run clean
rm -rf node_modules
rm -rf .turbo

echo "Clean complete!"
```

---

## Environment Configuration

### Environment Variables Template

```bash
# .env.example
NODE_ENV=development
PORT=3000

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/mydb

# Authentication
JWT_SECRET=your-secret-key-here

# API Keys (if needed)
API_KEY=your-api-key
```

### .gitignore

```
# Dependencies
node_modules
.pnp
.pnp.js

# Testing
coverage

# Production
dist
build
.next
out

# Misc
.DS_Store
*.pem

# Debug
npm-debug.log*
yarn-debug.log*
yarn-error.log*
.pnpm-debug.log*

# Local env files
.env
.env*.local

# Vercel
.vercel

# Turbo
.turbo

# IDE
.vscode
.idea
*.swp
*.swo
*~

# OS
Thumbs.db
```

---

## Application Setup Examples

### Backend API (Express Example)

```json
// apps/api/package.json
{
  "name": "@monorepo/api",
  "version": "1.0.0",
  "scripts": {
    "dev": "tsx watch src/index.ts",
    "build": "tsc",
    "start": "node dist/index.js",
    "type-check": "tsc --noEmit"
  },
  "dependencies": {
    "@monorepo/config": "workspace:*",
    "@monorepo/database": "workspace:*",
    "@monorepo/types": "workspace:*",
    "@monorepo/utils": "workspace:*",
    "express": "^4.18.0"
  },
  "devDependencies": {
    "@types/express": "^4.17.0",
    "tsx": "^4.7.0",
    "typescript": "^5.4.0"
  }
}
```

```typescript
// apps/api/src/index.ts
import express from 'express';
import { config } from '@monorepo/config';
import { db } from '@monorepo/database';

const app = express();

app.use(express.json());

app.get('/health', (req, res) => {
  res.json({ status: 'ok' });
});

app.listen(config.port, () => {
  console.log(`API running on port ${config.port}`);
});
```

### Frontend App (Vite + React Example)

```json
// apps/web/package.json
{
  "name": "@monorepo/web",
  "version": "1.0.0",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "type-check": "tsc --noEmit"
  },
  "dependencies": {
    "@monorepo/types": "workspace:*",
    "@monorepo/utils": "workspace:*",
    "react": "^18.2.0",
    "react-dom": "^18.2.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@vitejs/plugin-react": "^4.2.0",
    "typescript": "^5.4.0",
    "vite": "^5.0.0"
  }
}
```

---

## Quick Start Commands

```bash
# 1. Install dependencies
pnpm install

# 2. Setup git hooks
pnpm prepare

# 3. Build all packages
pnpm build

# 4. Run development mode
pnpm dev

# 5. Format code
pnpm format

# 6. Type check
pnpm type-check

# 7. Run tests
pnpm test
```

---

## Runtime Flexibility

### Using Node.js

```json
{
  "engines": {
    "node": ">=18.0.0"
  }
}
```

### Using Bun

```json
{
  "engines": {
    "bun": ">=1.0.0"
  },
  "scripts": {
    "dev": "bun run --watch src/index.ts",
    "build": "bun build src/index.ts --outdir dist",
    "start": "bun dist/index.js"
  }
}
```

---

## Best Practices

1. **Dependency Management**: Use workspace protocol (`workspace:*`) for internal dependencies
2. **Type Safety**: Enable strict TypeScript settings across all packages
3. **Code Quality**: Enforce formatting and linting through git hooks
4. **Incremental Builds**: Use TypeScript project references for faster builds
5. **Caching**: Leverage Turborepo for intelligent build caching
6. **Environment Variables**: Validate env vars with Zod at runtime
7. **Database Migrations**: Version control all database schema changes
8. **Testing**: Co-locate tests with source code
9. **Documentation**: Keep README files updated in each package
10. **Versioning**: Use changesets for package versioning and changelogs

---

## Additional Tools Setup

### 1. Changesets - Version Management

Changesets helps manage versions and changelogs in a monorepo.

#### Installation

```bash
pnpm add -D @changesets/cli
pnpm changeset init
```

#### Configuration

```json
// .changeset/config.json
{
  "$schema": "https://unpkg.com/@changesets/config@3.0.0/schema.json",
  "changelog": "@changesets/cli/changelog",
  "commit": false,
  "fixed": [],
  "linked": [],
  "access": "restricted",
  "baseBranch": "main",
  "updateInternalDependencies": "patch",
  "ignore": []
}
```

#### Usage

```bash
# Create a changeset
pnpm changeset

# Version packages
pnpm changeset version

# Publish packages
pnpm changeset publish
```

#### Add to package.json

```json
{
  "scripts": {
    "changeset": "changeset",
    "version-packages": "changeset version",
    "release": "pnpm build && changeset publish"
  }
}
```

---

### 2. Commitlint - Conventional Commits

Enforce conventional commit messages for better changelog generation.

#### Installation

```bash
pnpm add -D @commitlint/cli @commitlint/config-conventional
```

#### Configuration

```javascript
// commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [
      2,
      'always',
      [
        'feat',     // New feature
        'fix',      // Bug fix
        'docs',     // Documentation
        'style',    // Formatting
        'refactor', // Code restructuring
        'perf',     // Performance
        'test',     // Tests
        'build',    // Build system
        'ci',       // CI/CD
        'chore',    // Maintenance
        'revert',   // Revert commit
      ],
    ],
    'scope-case': [2, 'always', 'kebab-case'],
    'subject-case': [2, 'always', 'sentence-case'],
  },
};
```

#### Update Husky Hook

```bash
# .husky/commit-msg
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

pnpm commitlint --edit $1
```

#### Example Commits

```bash
git commit -m "feat(auth): add user authentication"
git commit -m "fix(api): resolve CORS issue"
git commit -m "docs(readme): update installation steps"
```

---

### 3. Vitest - Unit Testing

Fast unit test framework powered by Vite.

#### Installation

```bash
pnpm add -D vitest @vitest/ui @vitest/coverage-v8
```

#### Configuration

```typescript
// configs/vitest/base.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'node',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
      exclude: [
        'node_modules/',
        'dist/',
        '**/*.config.{ts,js}',
        '**/*.d.ts',
      ],
    },
  },
});
```

#### Package Setup

```json
// packages/utils/package.json
{
  "scripts": {
    "test": "vitest run",
    "test:watch": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest run --coverage"
  }
}
```

#### Example Test

```typescript
// packages/utils/src/validation.test.ts
import { describe, it, expect } from 'vitest';
import { isValidEmail } from './validation';

describe('isValidEmail', () => {
  it('should validate correct email', () => {
    expect(isValidEmail('test@example.com')).toBe(true);
  });

  it('should reject invalid email', () => {
    expect(isValidEmail('invalid-email')).toBe(false);
  });

  it('should reject empty string', () => {
    expect(isValidEmail('')).toBe(false);
  });
});
```

#### Root Scripts

```json
{
  "scripts": {
    "test": "turbo run test",
    "test:watch": "turbo run test:watch",
    "test:coverage": "turbo run test:coverage"
  }
}
```

---

### 4. Playwright - E2E Testing

End-to-end testing framework for web applications.

#### Installation

```bash
pnpm add -D @playwright/test
pnpm exec playwright install
```

#### Configuration

```typescript
// playwright.config.ts
import { defineConfig, devices } from '@playwright/test';

export default defineConfig({
  testDir: './tests/e2e',
  fullyParallel: true,
  forbidOnly: !!process.env.CI,
  retries: process.env.CI ? 2 : 0,
  workers: process.env.CI ? 1 : undefined,
  reporter: 'html',
  use: {
    baseURL: 'http://localhost:3000',
    trace: 'on-first-retry',
  },
  projects: [
    {
      name: 'chromium',
      use: { ...devices['Desktop Chrome'] },
    },
    {
      name: 'firefox',
      use: { ...devices['Desktop Firefox'] },
    },
    {
      name: 'webkit',
      use: { ...devices['Desktop Safari'] },
    },
  ],
  webServer: {
    command: 'pnpm dev',
    url: 'http://localhost:3000',
    reuseExistingServer: !process.env.CI,
  },
});
```

#### Example E2E Test

```typescript
// tests/e2e/auth.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Authentication', () => {
  test('should login successfully', async ({ page }) => {
    await page.goto('/login');
    
    await page.fill('input[name="email"]', 'test@example.com');
    await page.fill('input[name="password"]', 'password123');
    await page.click('button[type="submit"]');
    
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('h1')).toContainText('Dashboard');
  });

  test('should show error for invalid credentials', async ({ page }) => {
    await page.goto('/login');
    
    await page.fill('input[name="email"]', 'wrong@example.com');
    await page.fill('input[name="password"]', 'wrongpass');
    await page.click('button[type="submit"]');
    
    await expect(page.locator('.error')).toBeVisible();
  });
});
```

#### Scripts

```json
{
  "scripts": {
    "test:e2e": "playwright test",
    "test:e2e:ui": "playwright test --ui",
    "test:e2e:debug": "playwright test --debug"
  }
}
```

---

### 5. Docker - Containerization

Containerize your applications for consistent deployment.

#### Root Dockerfile (Multi-stage)

```dockerfile
# Dockerfile
FROM node:20-alpine AS base
RUN corepack enable pnpm

# Dependencies stage
FROM base AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
COPY apps/api/package.json ./apps/api/
COPY packages/*/package.json ./packages/
RUN pnpm install --frozen-lockfile

# Builder stage
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN pnpm build

# Runner stage
FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 appuser

COPY --from=builder --chown=appuser:nodejs /app/apps/api/dist ./dist
COPY --from=builder --chown=appuser:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=appuser:nodejs /app/package.json ./

USER appuser
EXPOSE 3000

CMD ["node", "dist/index.js"]
```

#### Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydb
    ports:
      - '5432:5432'
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - '6379:6379'
    volumes:
      - redis_data:/data

  api:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - '3000:3000'
    environment:
      NODE_ENV: production
      DATABASE_URL: postgresql://myuser:mypassword@postgres:5432/mydb
      REDIS_URL: redis://redis:6379
    depends_on:
      - postgres
      - redis

volumes:
  postgres_data:
  redis_data:
```

#### Development Docker Compose

```yaml
# docker-compose.dev.yml
version: '3.8'

services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_USER: myuser
      POSTGRES_PASSWORD: mypassword
      POSTGRES_DB: mydb
    ports:
      - '5432:5432'
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine
    ports:
      - '6379:6379'

volumes:
  postgres_data:
```

#### .dockerignore

```
node_modules
dist
build
.git
.gitignore
.env
.env*.local
*.log
coverage
.turbo
.next
README.md
```

#### Scripts

```json
{
  "scripts": {
    "docker:build": "docker build -t my-monorepo .",
    "docker:up": "docker-compose up -d",
    "docker:down": "docker-compose down",
    "docker:dev": "docker-compose -f docker-compose.dev.yml up -d"
  }
}
```

---

### 6. GitHub Actions - CI/CD Pipeline

Automate testing, building, and deployment.

#### Main CI Workflow

```yaml
# .github/workflows/ci.yml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  lint:
    name: Lint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: pnpm/action-setup@v2
        with:
          version: 8

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Run linter
        run: pnpm lint

      - name: Check formatting
        run: pnpm format:check

  type-check:
    name: Type Check
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: pnpm/action-setup@v2
        with:
          version: 8

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Type check
        run: pnpm type-check

  test:
    name: Test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: pnpm/action-setup@v2
        with:
          version: 8

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Run tests
        run: pnpm test:coverage

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          files: ./coverage/coverage-final.json

  build:
    name: Build
    runs-on: ubuntu-latest
    needs: [lint, type-check, test]
    steps:
      - uses: actions/checkout@v4

      - uses: pnpm/action-setup@v2
        with:
          version: 8

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Build
        run: pnpm build

      - name: Upload build artifacts
        uses: actions/upload-artifact@v4
        with:
          name: build
          path: |
            apps/*/dist
            packages/*/dist
```

#### E2E Testing Workflow

```yaml
# .github/workflows/e2e.yml
name: E2E Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  e2e:
    name: E2E Tests
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: pnpm/action-setup@v2
        with:
          version: 8

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Install Playwright browsers
        run: pnpm exec playwright install --with-deps

      - name: Build
        run: pnpm build

      - name: Run E2E tests
        run: pnpm test:e2e

      - name: Upload test results
        if: always()
        uses: actions/upload-artifact@v4
        with:
          name: playwright-report
          path: playwright-report/
```

#### Release Workflow

```yaml
# .github/workflows/release.yml
name: Release

on:
  push:
    branches:
      - main

concurrency: ${{ github.workflow }}-${{ github.ref }}

jobs:
  release:
    name: Release
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: pnpm/action-setup@v2
        with:
          version: 8

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Build
        run: pnpm build

      - name: Create Release Pull Request or Publish
        id: changesets
        uses: changesets/action@v1
        with:
          publish: pnpm release
          commit: 'chore: release packages'
          title: 'chore: release packages'
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          NPM_TOKEN: ${{ secrets.NPM_TOKEN }}
```

#### Docker Build and Push

```yaml
# .github/workflows/docker.yml
name: Docker

on:
  push:
    branches: [main]
    tags: ['v*']

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
      - uses: actions/checkout@v4

      - name: Log in to Container Registry
        uses: docker/login-action@v3
        with:
          registry: ${{ env.REGISTRY }}
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Extract metadata
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}
          tags: |
            type=ref,event=branch
            type=semver,pattern={{version}}
            type=semver,pattern={{major}}.{{minor}}
            type=sha

      - name: Build and push
        uses: docker/build-push-action@v5
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```

#### Dependabot Configuration

```yaml
# .github/dependabot.yml
version: 2
updates:
  - package-ecosystem: 'npm'
    directory: '/'
    schedule:
      interval: 'weekly'
    open-pull-requests-limit: 10
    groups:
      dev-dependencies:
        patterns:
          - '@types/*'
          - 'eslint*'
          - 'prettier'
          - 'typescript'

  - package-ecosystem: 'github-actions'
    directory: '/'
    schedule:
      interval: 'weekly'
```

---

## Complete Setup Checklist

- [ ] Initialize monorepo with package manager
- [ ] Configure TypeScript with project references
- [ ] Setup Prettier and ESLint
- [ ] Install and configure Husky
- [ ] Setup lint-staged for pre-commit hooks
- [ ] Configure Commitlint for conventional commits
- [ ] Initialize Changesets for version management
- [ ] Setup Vitest for unit testing
- [ ] Configure Playwright for E2E testing
- [ ] Create Dockerfile and docker-compose.yml
- [ ] Setup GitHub Actions workflows
- [ ] Configure Dependabot
- [ ] Create shared packages (types, utils, config, database)
- [ ] Setup Turborepo for build orchestration
- [ ] Add environment variable validation
- [ ] Create documentation

---

## Conclusion

This setup provides a solid foundation for a scalable TypeScript monorepo that is:
- Framework agnostic (works with any TS frontend/backend)
- Runtime agnostic (Node.js or Bun)
- Type-safe across all packages
- Maintainable with proper tooling
- Production-ready with quality gates
- Fully tested with unit and E2E tests
- Containerized for consistent deployment
- Automated with CI/CD pipelines

---

## Init Constraint

When instantiating from this doc, you should always ask:
- What frameworks will user use?
- What runtime?
- What is the name of the project?
- What orm / query builder will user use?
