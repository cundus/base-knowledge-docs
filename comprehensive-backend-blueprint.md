# Comprehensive Backend Blueprint

A complete guide for setting up modern, production-ready backend applications with TypeScript, supporting multiple frameworks, databases, and architectural patterns.

## Table of Contents

1. [Project Initialization Questions](#project-initialization-questions)
2. [Project Structure](#project-structure)
3. [Framework-Specific Setup](#framework-specific-setup)
4. [Database Setup](#database-setup)
5. [Authentication & Authorization](#authentication--authorization)
6. [API Documentation](#api-documentation)
7. [Validation & Error Handling](#validation--error-handling)
8. [File Upload & Storage](#file-upload--storage)
9. [Caching](#caching)
10. [Background Jobs](#background-jobs)
11. [Testing](#testing)
12. [Logging & Monitoring](#logging--monitoring)
13. [Security](#security)
14. [Deployment](#deployment)

---

## Project Initialization Questions

Before starting, answer these questions to customize your setup:

### Required Questions

1. **What runtime do you want to use?**
   - Node.js
   - Bun
   - Deno

2. **What backend framework do you prefer?**
   
   **For Node.js/Bun:**
   - Express (traditional, widely used)
   - Fastify (high performance)
   - Hono (modern, edge-ready)
   - NestJS (enterprise, Angular-like)
   - Koa (minimalist)
   - tRPC (type-safe, full-stack)
   - Elysia (Bun-optimized)

   **For Deno:**
   - Fresh
   - Oak
   - Hono

3. **What database do you want to use?**
   - PostgreSQL (recommended for relational)
   - MySQL/MariaDB
   - MongoDB
   - SQLite
   - Redis (cache/session store)
   - Multiple databases

4. **What ORM/Query Builder do you prefer?**
   
   **For SQL:**
   - Drizzle ORM (recommended, type-safe)
   - Prisma (popular, great DX)
   - TypeORM (feature-rich)
   - Kysely (type-safe query builder)
   - Knex.js (query builder)
   - Raw SQL

   **For MongoDB:**
   - Mongoose
   - Prisma
   - MongoDB Driver

5. **What authentication strategy?**
   - JWT (JSON Web Tokens)
   - Session-based
   - OAuth 2.0 / Social Login
   - Passport.js
   - Auth0
   - Clerk
   - Supabase Auth
   - None

### Optional Questions

6. **Do you need real-time features?**
   - Yes (WebSockets)
   - Yes (Server-Sent Events)
   - Yes (Socket.io)
   - No

7. **What validation library?**
   - Zod (recommended)
   - Yup
   - Joi
   - class-validator
   - AJV

8. **Do you need file upload?**
   - Yes (Local storage)
   - Yes (AWS S3)
   - Yes (Cloudinary)
   - Yes (Google Cloud Storage)
   - No

9. **Do you need caching?**
   - Yes (Redis)
   - Yes (In-memory)
   - No

10. **Do you need background jobs?**
    - Yes (BullMQ)
    - Yes (Agenda)
    - Yes (node-cron)
    - No

11. **What API documentation?**
    - Swagger/OpenAPI
    - Scalar
    - Postman
    - None

12. **Do you need rate limiting?**
    - Yes
    - No

13. **What logging solution?**
    - Pino (recommended for performance)
    - Winston
    - Morgan
    - Console only

14. **Do you need email service?**
    - Yes (Nodemailer)
    - Yes (SendGrid)
    - Yes (Resend)
    - Yes (AWS SES)
    - No

15. **Project type?**
    - Part of monorepo
    - Standalone project

16. **API architecture?**
    - REST API
    - GraphQL
    - tRPC
    - gRPC
    - Mixed

---

## Project Structure

### Standalone Project Structure

```
backend/
├── src/
│   ├── config/              # Configuration files
│   │   ├── database.ts
│   │   ├── env.ts
│   │   └── logger.ts
│   ├── modules/             # Feature modules
│   │   ├── auth/
│   │   │   ├── auth.controller.ts
│   │   │   ├── auth.service.ts
│   │   │   ├── auth.routes.ts
│   │   │   ├── auth.schema.ts
│   │   │   └── auth.test.ts
│   │   ├── users/
│   │   └── posts/
│   ├── shared/              # Shared utilities
│   │   ├── middleware/
│   │   ├── utils/
│   │   ├── types/
│   │   └── constants/
│   ├── database/            # Database related
│   │   ├── migrations/
│   │   ├── seeds/
│   │   └── schema.ts
│   ├── jobs/                # Background jobs
│   ├── app.ts               # App setup
│   └── server.ts            # Server entry
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── .env.example
├── .eslintrc.js
├── .prettierrc.js
├── package.json
├── tsconfig.json
└── README.md
```


### Monorepo Integration Structure

```
apps/
└── api/                     # Backend app
    ├── src/
    │   ├── config/
    │   ├── modules/
    │   ├── shared/
    │   ├── app.ts
    │   └── server.ts
    ├── package.json
    └── tsconfig.json
```

---

## Framework-Specific Setup

### Express + TypeScript

#### Installation

```bash
pnpm add express
pnpm add -D @types/express tsx nodemon
```

#### package.json

```json
{
  "name": "backend-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "test": "vitest",
    "lint": "eslint src --ext .ts",
    "type-check": "tsc --noEmit"
  },
  "dependencies": {
    "express": "^4.18.0",
    "dotenv": "^16.0.0",
    "zod": "^3.22.0"
  },
  "devDependencies": {
    "@types/express": "^4.17.0",
    "@types/node": "^20.0.0",
    "tsx": "^4.7.0",
    "typescript": "^5.4.0"
  }
}
```

#### src/server.ts

```typescript
import express from 'express';
import { config } from './config/env';
import { logger } from './config/logger';
import { errorHandler } from './shared/middleware/errorHandler';
import { authRoutes } from './modules/auth/auth.routes';
import { userRoutes } from './modules/users/user.routes';

const app = express();

// Middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.use('/api/auth', authRoutes);
app.use('/api/users', userRoutes);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});

// Error handling
app.use(errorHandler);

app.listen(config.port, () => {
  logger.info(`Server running on port ${config.port}`);
});
```

#### src/config/env.ts

```typescript
import { z } from 'zod';
import dotenv from 'dotenv';

dotenv.config();

const envSchema = z.object({
  NODE_ENV: z.enum(['development', 'production', 'test']).default('development'),
  PORT: z.string().default('4000'),
  DATABASE_URL: z.string(),
  JWT_SECRET: z.string(),
  JWT_EXPIRES_IN: z.string().default('7d'),
  REDIS_URL: z.string().optional(),
});

const parsed = envSchema.safeParse(process.env);

if (!parsed.success) {
  console.error('❌ Invalid environment variables:', parsed.error.flatten().fieldErrors);
  process.exit(1);
}

export const config = {
  env: parsed.data.NODE_ENV,
  port: parseInt(parsed.data.PORT, 10),
  database: {
    url: parsed.data.DATABASE_URL,
  },
  jwt: {
    secret: parsed.data.JWT_SECRET,
    expiresIn: parsed.data.JWT_EXPIRES_IN,
  },
  redis: {
    url: parsed.data.REDIS_URL,
  },
} as const;
```


### Fastify + TypeScript

#### Installation

```bash
pnpm add fastify @fastify/cors @fastify/helmet
pnpm add -D @types/node tsx
```

#### src/server.ts

```typescript
import Fastify from 'fastify';
import cors from '@fastify/cors';
import helmet from '@fastify/helmet';
import { config } from './config/env';
import { authRoutes } from './modules/auth/auth.routes';

const fastify = Fastify({
  logger: true,
});

// Plugins
fastify.register(cors);
fastify.register(helmet);

// Routes
fastify.register(authRoutes, { prefix: '/api/auth' });

// Health check
fastify.get('/health', async () => {
  return { status: 'ok', timestamp: new Date().toISOString() };
});

const start = async () => {
  try {
    await fastify.listen({ port: config.port, host: '0.0.0.0' });
  } catch (err) {
    fastify.log.error(err);
    process.exit(1);
  }
};

start();
```

### Hono + TypeScript

#### Installation

```bash
pnpm add hono
pnpm add -D @types/node tsx
```

#### src/server.ts

```typescript
import { Hono } from 'hono';
import { cors } from 'hono/cors';
import { logger } from 'hono/logger';
import { authRoutes } from './modules/auth/auth.routes';
import { config } from './config/env';

const app = new Hono();

// Middleware
app.use('*', logger());
app.use('*', cors());

// Routes
app.route('/api/auth', authRoutes);

// Health check
app.get('/health', (c) => {
  return c.json({ status: 'ok', timestamp: new Date().toISOString() });
});

export default {
  port: config.port,
  fetch: app.fetch,
};
```

### NestJS Setup

#### Installation

```bash
pnpm add @nestjs/common @nestjs/core @nestjs/platform-express reflect-metadata rxjs
pnpm add -D @nestjs/cli @nestjs/schematics
```

#### src/main.ts

```typescript
import { NestFactory } from '@nestjs/core';
import { ValidationPipe } from '@nestjs/common';
import { AppModule } from './app.module';
import { config } from './config/env';

async function bootstrap() {
  const app = await NestFactory.create(AppModule);
  
  app.useGlobalPipes(new ValidationPipe({
    whitelist: true,
    forbidNonWhitelisted: true,
    transform: true,
  }));
  
  app.setGlobalPrefix('api');
  
  await app.listen(config.port);
  console.log(`Application running on port ${config.port}`);
}

bootstrap();
```

---

## Database Setup

### Drizzle ORM + PostgreSQL

#### Installation

```bash
pnpm add drizzle-orm postgres
pnpm add -D drizzle-kit
```

#### drizzle.config.ts

```typescript
import type { Config } from 'drizzle-kit';
import { config } from './src/config/env';

export default {
  schema: './src/database/schema.ts',
  out: './src/database/migrations',
  driver: 'pg',
  dbCredentials: {
    connectionString: config.database.url,
  },
} satisfies Config;
```

#### src/database/schema.ts

```typescript
import { pgTable, uuid, varchar, timestamp, text, boolean } from 'drizzle-orm/pg-core';

export const users = pgTable('users', {
  id: uuid('id').defaultRandom().primaryKey(),
  email: varchar('email', { length: 255 }).notNull().unique(),
  name: varchar('name', { length: 255 }).notNull(),
  passwordHash: text('password_hash').notNull(),
  emailVerified: boolean('email_verified').default(false),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull(),
});

export const posts = pgTable('posts', {
  id: uuid('id').defaultRandom().primaryKey(),
  title: varchar('title', { length: 255 }).notNull(),
  content: text('content').notNull(),
  published: boolean('published').default(false),
  authorId: uuid('author_id').references(() => users.id).notNull(),
  createdAt: timestamp('created_at').defaultNow().notNull(),
  updatedAt: timestamp('updated_at').defaultNow().notNull(),
});
```

#### src/database/client.ts

```typescript
import { drizzle } from 'drizzle-orm/postgres-js';
import postgres from 'postgres';
import { config } from '../config/env';
import * as schema from './schema';

const client = postgres(config.database.url);
export const db = drizzle(client, { schema });
```

#### package.json scripts

```json
{
  "scripts": {
    "db:generate": "drizzle-kit generate:pg",
    "db:migrate": "drizzle-kit push:pg",
    "db:studio": "drizzle-kit studio"
  }
}
```


### Prisma + PostgreSQL

#### Installation

```bash
pnpm add @prisma/client
pnpm add -D prisma
```

#### Initialize Prisma

```bash
pnpm prisma init
```

#### prisma/schema.prisma

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

model User {
  id            String   @id @default(uuid())
  email         String   @unique
  name          String
  passwordHash  String   @map("password_hash")
  emailVerified Boolean  @default(false) @map("email_verified")
  createdAt     DateTime @default(now()) @map("created_at")
  updatedAt     DateTime @updatedAt @map("updated_at")
  posts         Post[]

  @@map("users")
}

model Post {
  id        String   @id @default(uuid())
  title     String
  content   String
  published Boolean  @default(false)
  authorId  String   @map("author_id")
  author    User     @relation(fields: [authorId], references: [id])
  createdAt DateTime @default(now()) @map("created_at")
  updatedAt DateTime @updatedAt @map("updated_at")

  @@map("posts")
}
```

#### src/database/client.ts

```typescript
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as {
  prisma: PrismaClient | undefined;
};

export const prisma = globalForPrisma.prisma ?? new PrismaClient({
  log: ['query', 'error', 'warn'],
});

if (process.env.NODE_ENV !== 'production') {
  globalForPrisma.prisma = prisma;
}
```

#### package.json scripts

```json
{
  "scripts": {
    "db:generate": "prisma generate",
    "db:migrate": "prisma migrate dev",
    "db:studio": "prisma studio",
    "db:seed": "tsx prisma/seed.ts"
  }
}
```

---

## Authentication & Authorization

### JWT Authentication

#### Installation

```bash
pnpm add jsonwebtoken bcrypt
pnpm add -D @types/jsonwebtoken @types/bcrypt
```

#### src/modules/auth/auth.service.ts

```typescript
import bcrypt from 'bcrypt';
import jwt from 'jsonwebtoken';
import { db } from '../../database/client';
import { users } from '../../database/schema';
import { eq } from 'drizzle-orm';
import { config } from '../../config/env';

export class AuthService {
  async register(email: string, password: string, name: string) {
    const existingUser = await db.query.users.findFirst({
      where: eq(users.email, email),
    });

    if (existingUser) {
      throw new Error('User already exists');
    }

    const passwordHash = await bcrypt.hash(password, 10);

    const [user] = await db.insert(users).values({
      email,
      name,
      passwordHash,
    }).returning();

    const token = this.generateToken(user.id);

    return {
      user: {
        id: user.id,
        email: user.email,
        name: user.name,
      },
      token,
    };
  }

  async login(email: string, password: string) {
    const user = await db.query.users.findFirst({
      where: eq(users.email, email),
    });

    if (!user) {
      throw new Error('Invalid credentials');
    }

    const isValidPassword = await bcrypt.compare(password, user.passwordHash);

    if (!isValidPassword) {
      throw new Error('Invalid credentials');
    }

    const token = this.generateToken(user.id);

    return {
      user: {
        id: user.id,
        email: user.email,
        name: user.name,
      },
      token,
    };
  }

  private generateToken(userId: string): string {
    return jwt.sign({ userId }, config.jwt.secret, {
      expiresIn: config.jwt.expiresIn,
    });
  }

  verifyToken(token: string) {
    try {
      return jwt.verify(token, config.jwt.secret) as { userId: string };
    } catch {
      throw new Error('Invalid token');
    }
  }
}
```

#### src/shared/middleware/auth.middleware.ts

```typescript
import { Request, Response, NextFunction } from 'express';
import { AuthService } from '../../modules/auth/auth.service';

const authService = new AuthService();

export interface AuthRequest extends Request {
  userId?: string;
}

export const authMiddleware = async (
  req: AuthRequest,
  res: Response,
  next: NextFunction
) => {
  try {
    const authHeader = req.headers.authorization;

    if (!authHeader || !authHeader.startsWith('Bearer ')) {
      return res.status(401).json({ error: 'Unauthorized' });
    }

    const token = authHeader.substring(7);
    const { userId } = authService.verifyToken(token);

    req.userId = userId;
    next();
  } catch (error) {
    return res.status(401).json({ error: 'Unauthorized' });
  }
};
```

#### src/modules/auth/auth.routes.ts

```typescript
import { Router } from 'express';
import { AuthController } from './auth.controller';
import { validateRequest } from '../../shared/middleware/validation';
import { registerSchema, loginSchema } from './auth.schema';

const router = Router();
const authController = new AuthController();

router.post('/register', validateRequest(registerSchema), authController.register);
router.post('/login', validateRequest(loginSchema), authController.login);

export { router as authRoutes };
```

#### src/modules/auth/auth.schema.ts

```typescript
import { z } from 'zod';

export const registerSchema = z.object({
  body: z.object({
    email: z.string().email('Invalid email address'),
    password: z.string().min(8, 'Password must be at least 8 characters'),
    name: z.string().min(2, 'Name must be at least 2 characters'),
  }),
});

export const loginSchema = z.object({
  body: z.object({
    email: z.string().email('Invalid email address'),
    password: z.string().min(1, 'Password is required'),
  }),
});
```

---

## API Documentation

### Swagger/OpenAPI

#### Installation

```bash
pnpm add swagger-ui-express swagger-jsdoc
pnpm add -D @types/swagger-ui-express @types/swagger-jsdoc
```

#### src/config/swagger.ts

```typescript
import swaggerJsdoc from 'swagger-jsdoc';
import { config } from './env';

const options = {
  definition: {
    openapi: '3.0.0',
    info: {
      title: 'API Documentation',
      version: '1.0.0',
      description: 'REST API documentation',
    },
    servers: [
      {
        url: `http://localhost:${config.port}`,
        description: 'Development server',
      },
    ],
    components: {
      securitySchemes: {
        bearerAuth: {
          type: 'http',
          scheme: 'bearer',
          bearerFormat: 'JWT',
        },
      },
    },
  },
  apis: ['./src/modules/**/*.routes.ts'],
};

export const swaggerSpec = swaggerJsdoc(options);
```

#### Usage in routes

```typescript
/**
 * @swagger
 * /api/auth/register:
 *   post:
 *     summary: Register a new user
 *     tags: [Auth]
 *     requestBody:
 *       required: true
 *       content:
 *         application/json:
 *           schema:
 *             type: object
 *             required:
 *               - email
 *               - password
 *               - name
 *             properties:
 *               email:
 *                 type: string
 *               password:
 *                 type: string
 *               name:
 *                 type: string
 *     responses:
 *       201:
 *         description: User registered successfully
 */
router.post('/register', authController.register);
```

#### Setup in server.ts

```typescript
import swaggerUi from 'swagger-ui-express';
import { swaggerSpec } from './config/swagger';

app.use('/api-docs', swaggerUi.serve, swaggerUi.setup(swaggerSpec));
```

---

## Validation & Error Handling

### Zod Validation Middleware

#### src/shared/middleware/validation.ts

```typescript
import { Request, Response, NextFunction } from 'express';
import { AnyZodObject, ZodError } from 'zod';

export const validateRequest = (schema: AnyZodObject) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    try {
      await schema.parseAsync({
        body: req.body,
        query: req.query,
        params: req.params,
      });
      next();
    } catch (error) {
      if (error instanceof ZodError) {
        return res.status(400).json({
          error: 'Validation failed',
          details: error.errors,
        });
      }
      next(error);
    }
  };
};
```

### Global Error Handler

#### src/shared/middleware/errorHandler.ts

```typescript
import { Request, Response, NextFunction } from 'express';
import { logger } from '../../config/logger';

export class AppError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public isOperational = true
  ) {
    super(message);
    Object.setPrototypeOf(this, AppError.prototype);
  }
}

export const errorHandler = (
  err: Error | AppError,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  if (err instanceof AppError) {
    logger.error({
      message: err.message,
      statusCode: err.statusCode,
      path: req.path,
    });

    return res.status(err.statusCode).json({
      error: err.message,
    });
  }

  logger.error({
    message: err.message,
    stack: err.stack,
    path: req.path,
  });

  return res.status(500).json({
    error: 'Internal server error',
  });
};
```

---

## File Upload & Storage

### Local File Upload with Multer

#### Installation

```bash
pnpm add multer
pnpm add -D @types/multer
```

#### src/config/multer.ts

```typescript
import multer from 'multer';
import path from 'path';
import { AppError } from '../shared/middleware/errorHandler';

const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/');
  },
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1e9);
    cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
  },
});

const fileFilter = (req: any, file: Express.Multer.File, cb: multer.FileFilterCallback) => {
  const allowedTypes = /jpeg|jpg|png|gif|pdf/;
  const extname = allowedTypes.test(path.extname(file.originalname).toLowerCase());
  const mimetype = allowedTypes.test(file.mimetype);

  if (mimetype && extname) {
    return cb(null, true);
  }
  cb(new AppError(400, 'Invalid file type'));
};

export const upload = multer({
  storage,
  fileFilter,
  limits: {
    fileSize: 5 * 1024 * 1024, // 5MB
  },
});
```

### AWS S3 Upload

#### Installation

```bash
pnpm add @aws-sdk/client-s3 @aws-sdk/s3-request-presigner
```

#### src/services/s3.service.ts

```typescript
import { S3Client, PutObjectCommand } from '@aws-sdk/client-s3';
import { getSignedUrl } from '@aws-sdk/s3-request-presigner';

const s3Client = new S3Client({
  region: process.env.AWS_REGION,
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID!,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY!,
  },
});

export class S3Service {
  async uploadFile(file: Express.Multer.File, key: string) {
    const command = new PutObjectCommand({
      Bucket: process.env.AWS_S3_BUCKET,
      Key: key,
      Body: file.buffer,
      ContentType: file.mimetype,
    });

    await s3Client.send(command);

    return {
      key,
      url: `https://${process.env.AWS_S3_BUCKET}.s3.${process.env.AWS_REGION}.amazonaws.com/${key}`,
    };
  }

  async getSignedUrl(key: string, expiresIn = 3600) {
    const command = new PutObjectCommand({
      Bucket: process.env.AWS_S3_BUCKET,
      Key: key,
    });

    return await getSignedUrl(s3Client, command, { expiresIn });
  }
}
```

---

## Caching

### Redis Setup

#### Installation

```bash
pnpm add ioredis
```

#### src/config/redis.ts

```typescript
import Redis from 'ioredis';
import { config } from './env';
import { logger } from './logger';

export const redis = new Redis(config.redis.url || 'redis://localhost:6379', {
  maxRetriesPerRequest: 3,
  retryStrategy: (times) => {
    const delay = Math.min(times * 50, 2000);
    return delay;
  },
});

redis.on('connect', () => {
  logger.info('Redis connected');
});

redis.on('error', (err) => {
  logger.error('Redis error:', err);
});
```

#### Cache Middleware

```typescript
import { Request, Response, NextFunction } from 'express';
import { redis } from '../../config/redis';

export const cacheMiddleware = (duration: number) => {
  return async (req: Request, res: Response, next: NextFunction) => {
    if (req.method !== 'GET') {
      return next();
    }

    const key = `cache:${req.originalUrl}`;

    try {
      const cached = await redis.get(key);

      if (cached) {
        return res.json(JSON.parse(cached));
      }

      const originalJson = res.json.bind(res);
      res.json = (data: any) => {
        redis.setex(key, duration, JSON.stringify(data));
        return originalJson(data);
      };

      next();
    } catch (error) {
      next();
    }
  };
};
```

---

## Background Jobs

### BullMQ Setup

#### Installation

```bash
pnpm add bullmq
```

#### src/jobs/email.queue.ts

```typescript
import { Queue, Worker } from 'bullmq';
import { redis } from '../config/redis';
import { logger } from '../config/logger';

interface EmailJob {
  to: string;
  subject: string;
  body: string;
}

export const emailQueue = new Queue<EmailJob>('email', {
  connection: redis,
});

export const emailWorker = new Worker<EmailJob>(
  'email',
  async (job) => {
    logger.info(`Processing email job ${job.id}`);
    
    // Send email logic here
    await sendEmail(job.data);
    
    logger.info(`Email sent to ${job.data.to}`);
  },
  {
    connection: redis,
  }
);

emailWorker.on('completed', (job) => {
  logger.info(`Job ${job.id} completed`);
});

emailWorker.on('failed', (job, err) => {
  logger.error(`Job ${job?.id} failed:`, err);
});

async function sendEmail(data: EmailJob) {
  // Email sending implementation
}
```

#### Usage

```typescript
import { emailQueue } from '../jobs/email.queue';

await emailQueue.add('welcome-email', {
  to: user.email,
  subject: 'Welcome!',
  body: 'Welcome to our platform',
});
```

---

## Testing

### Vitest + Supertest

#### Installation

```bash
pnpm add -D vitest supertest @types/supertest
```

#### vitest.config.ts

```typescript
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'node',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
    },
  },
});
```

#### tests/integration/auth.test.ts

```typescript
import { describe, it, expect, beforeAll, afterAll } from 'vitest';
import request from 'supertest';
import { app } from '../../src/app';
import { db } from '../../src/database/client';

describe('Auth API', () => {
  beforeAll(async () => {
    // Setup test database
  });

  afterAll(async () => {
    // Cleanup
  });

  describe('POST /api/auth/register', () => {
    it('should register a new user', async () => {
      const response = await request(app)
        .post('/api/auth/register')
        .send({
          email: 'test@example.com',
          password: 'password123',
          name: 'Test User',
        });

      expect(response.status).toBe(201);
      expect(response.body).toHaveProperty('token');
      expect(response.body.user).toHaveProperty('email', 'test@example.com');
    });

    it('should return 400 for invalid email', async () => {
      const response = await request(app)
        .post('/api/auth/register')
        .send({
          email: 'invalid-email',
          password: 'password123',
          name: 'Test User',
        });

      expect(response.status).toBe(400);
    });
  });
});
```

---

## Logging & Monitoring

### Pino Logger

#### Installation

```bash
pnpm add pino pino-pretty
```

#### src/config/logger.ts

```typescript
import pino from 'pino';
import { config } from './env';

export const logger = pino({
  level: config.env === 'production' ? 'info' : 'debug',
  transport: config.env !== 'production' ? {
    target: 'pino-pretty',
    options: {
      colorize: true,
      translateTime: 'SYS:standard',
      ignore: 'pid,hostname',
    },
  } : undefined,
});
```

#### Request Logger Middleware

```typescript
import { Request, Response, NextFunction } from 'express';
import { logger } from '../../config/logger';

export const requestLogger = (req: Request, res: Response, next: NextFunction) => {
  const start = Date.now();

  res.on('finish', () => {
    const duration = Date.now() - start;
    logger.info({
      method: req.method,
      path: req.path,
      status: res.statusCode,
      duration: `${duration}ms`,
    });
  });

  next();
};
```

---

## Security

### Security Best Practices

#### Installation

```bash
pnpm add helmet cors express-rate-limit
```

#### src/config/security.ts

```typescript
import helmet from 'helmet';
import cors from 'cors';
import rateLimit from 'express-rate-limit';

export const securityMiddleware = [
  helmet(),
  cors({
    origin: process.env.ALLOWED_ORIGINS?.split(',') || '*',
    credentials: true,
  }),
];

export const rateLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP',
});

export const authRateLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: 'Too many login attempts',
});
```

#### Usage

```typescript
import { securityMiddleware, rateLimiter, authRateLimiter } from './config/security';

app.use(securityMiddleware);
app.use('/api', rateLimiter);
app.use('/api/auth/login', authRateLimiter);
```

---

## Deployment

### Docker

#### Dockerfile

```dockerfile
FROM node:20-alpine AS base
RUN corepack enable pnpm

FROM base AS deps
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile --prod

FROM base AS builder
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN pnpm install --frozen-lockfile
COPY . .
RUN pnpm build

FROM base AS runner
WORKDIR /app
ENV NODE_ENV=production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 apiuser

COPY --from=deps --chown=apiuser:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=apiuser:nodejs /app/dist ./dist
COPY --from=builder --chown=apiuser:nodejs /app/package.json ./

USER apiuser
EXPOSE 4000

CMD ["node", "dist/server.js"]
```

### Environment Variables

```bash
# .env.example
NODE_ENV=production
PORT=4000

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/mydb

# JWT
JWT_SECRET=your-super-secret-key
JWT_EXPIRES_IN=7d

# Redis
REDIS_URL=redis://localhost:6379

# AWS (if using S3)
AWS_REGION=us-east-1
AWS_ACCESS_KEY_ID=your-access-key
AWS_SECRET_ACCESS_KEY=your-secret-key
AWS_S3_BUCKET=your-bucket-name

# Email (if using)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-password

# CORS
ALLOWED_ORIGINS=http://localhost:3000,https://yourdomain.com
```

---

## Complete Setup Checklist

- [ ] Answer initialization questions
- [ ] Setup project structure
- [ ] Configure runtime and framework
- [ ] Setup database and ORM
- [ ] Implement authentication
- [ ] Add validation middleware
- [ ] Setup error handling
- [ ] Configure logging
- [ ] Add security middleware
- [ ] Setup API documentation
- [ ] Implement caching (if needed)
- [ ] Setup background jobs (if needed)
- [ ] Configure file upload (if needed)
- [ ] Write tests
- [ ] Setup CI/CD
- [ ] Configure deployment

---

## Quick Start Commands

```json
{
  "scripts": {
    "dev": "tsx watch src/server.ts",
    "build": "tsc",
    "start": "node dist/server.js",
    "test": "vitest",
    "test:coverage": "vitest run --coverage",
    "lint": "eslint src --ext .ts",
    "lint:fix": "eslint src --ext .ts --fix",
    "format": "prettier --write \"src/**/*.ts\"",
    "type-check": "tsc --noEmit",
    "db:generate": "drizzle-kit generate",
    "db:migrate": "drizzle-kit push",
    "db:studio": "drizzle-kit studio"
  }
}
```

---

## Conclusion

This blueprint provides a comprehensive foundation for modern backend development with TypeScript. Customize based on your specific requirements and scale as needed.
