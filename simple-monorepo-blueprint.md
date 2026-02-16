This design document outlines the architecture for a **Universal TypeScript Monorepo Initiator**. This blueprint is framework-agnostic (React, Vue, NestJS, Express, etc.) and runtime-agnostic (Node.js or Bun), emphasizing modularity and strict type safety.

### **1. Architectural Overview**

The monorepo uses a workspace-based architecture. To maintain agility, configuration and core logic are decoupled from individual applications.

**Core Principles:**

* **Single Source of Truth:** Shared types and configurations live in dedicated packages.
* **Isolation:** Applications (`apps/*`) depend on `packages/*`, never on each other.
* **Tooling Consistency:** Husky, Prettier, and ESLint rules are enforced at the root but extendable per package.

---

### **2. Directory Structure**

This structure segregates infrastructure code from application code.

```text
my-monorepo/
├── .husky/                 # Git hooks (pre-commit, commit-msg)
├── .vscode/                # Shared VS Code settings
├── apps/                   # Application layer (Framework Agnostic)
│   ├── backend/            # e.g., Express, Fastify, Hono
│   └── frontend/           # e.g., Next.js, Vite, Astro
├── packages/               # Shared logic layer
│   ├── config/             # Shared configurations (TS, ESLint)
│   ├── database/           # ORM, Schema, Migrations (Prisma/Drizzle)
│   ├── logger/             # Shared logging utility
│   ├── types/              # Pure TS types/interfaces (Zod schemas)
│   └── ui/                 # Shared UI Component Library (optional)
├── tools/                  # Build scripts & local CLI tools
│   └── scaffold/           # Scripts to generate new packages/apps
├── .gitignore
├── .npmrc                  # Dependency hoisting configs
├── .prettierrc.js          # Root prettier config
├── package.json            # Root scripts & devDependencies
├── pnpm-workspace.yaml     # Workspace definition (if using pnpm)
├── tsconfig.json           # Base TS config
└── turbo.json              # Turborepo config (optional but recommended)

```

---

### **3. Configuration Strategy (The `packages/config` Pattern)**

Instead of repeating configs, we create a specialized package that exports base configurations. This allows any new app to "inherit" the standard setup instantly.

#### **A. File Structure (`packages/config/`)**

```text
packages/config/
├── eslint/
│   ├── base.js
│   ├── next.js
│   └── node.js
├── typescript/
│   ├── base.json
│   ├── react.json
│   └── node.json
└── package.json

```

#### **B. Usage Example**

An app's `tsconfig.json` simply extends the shared config:

```json
{
  "extends": "@repo/config/typescript/react.json",
  "compilerOptions": {
    "outDir": "dist"
  }
}

```

---

### **4. Database Isolation (`packages/database`)**

Treating the database as an isolated package allows both the backend API and separate worker scripts to access the DB safely without circular dependencies.

* **Runtime Agnostic:** Use an ORM like **Prisma** or **Drizzle** that works in Node and Bun.
* **Exports:** The package exports the initialized client and generated types.

**`packages/database/package.json`**

```json
{
  "name": "@repo/database",
  "exports": {
    ".": "./src/index.ts"
  },
  "scripts": {
    "db:push": "drizzle-kit push:pg",
    "db:studio": "drizzle-kit studio"
  }
}

```

---

### **5. Tooling & Quality Assurance**

#### **Husky & Lint-Staged Setup**

This setup ensures no bad code enters the repo. It runs formatting and type-checking only on changed files.

**`.husky/pre-commit`**

```sh
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

pnpm exec lint-staged

```

**`package.json` (Root)**

```json
"lint-staged": {
  "*.{ts,tsx,js,jsx}": [
    "prettier --write",
    "eslint --fix"
  ],
  "*.{json,md,yml}": [
    "prettier --write"
  ]
}

```

---

### **6. Shared Types (`packages/types`)**

This is the "glue" of the monorepo. It ensures the Frontend and Backend speak the same language.

* **Recommendation:** Use **Zod** for runtime validation and type inference.
* **Workflow:**
1. Define a DTO (Data Transfer Object) in `packages/types`.
2. Import schema in `apps/backend` to validate API requests.
3. Import inferred type in `apps/frontend` to type API responses.



**`packages/types/src/auth.ts`**

```typescript
import { z } from "zod";

export const LoginSchema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

export type LoginRequest = z.infer<typeof LoginSchema>;

```

---

### **7. The "Initiator" Script Logic**

If you are building a CLI tool to generate this structure, it should perform the following operations in order:

1. **Detection:**
* Check for `bun` availability; fallback to `node`/`npm` or `pnpm`.


2. **Scaffolding:**
* Create root directory.
* Write `pnpm-workspace.yaml` (or `workspaces` in package.json).


3. **Dependency Installation:**
* Install `typescript`, `eslint`, `prettier`, `husky`, `turbo` as dev dependencies at root.


4. **Package Linking:**
* Create the `packages/config` folder.
* Write the base `tsconfig.json` files.


5. **Git Initialization:**
* `git init`
* `npx husky install`


6. **Final Output:**
* Print success message with commands: "Run `pnpm install` to get started."



---

### **8. Recommended `tsconfig.base.json**`

A strict base config to ensure quality across the monorepo.

```json
{
  "compilerOptions": {
    "composite": false,
    "declaration": true,
    "declarationMap": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": true,
    "inlineSources": false,
    "isolatedModules": true,
    "moduleResolution": "bundler",
    "noUnusedLocals": false,
    "noUnusedParameters": false,
    "preserveWatchOutput": true,
    "skipLibCheck": true,
    "strict": true
  }
}

```