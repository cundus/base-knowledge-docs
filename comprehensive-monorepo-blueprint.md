
# TypeScript Monorepo Project Initiator Builder (Revised Architecture)

## 1. High Level Philosophy

Starter ini didesain untuk:

* Framework agnostic (bebas pakai FE / BE framework TS apa pun)
* Runtime agnostic (Node / Bun ready)
* Config centralized (semua config reusable & versioned)
* Database as shared infrastructure package
* Strong shared contract (types + validation + db models)
* Production scale ready

---

# 2. Revised Repository Structure

```
repo-root/
├ apps/
│ ├ web/
│ ├ mobile/
│ ├ admin/
│ └ api/
│
├ packages/
│ ├ db/
│ ├ types/
│ ├ validation/
│ ├ utils/
│ ├ ui/
│
├ configs/
│ ├ eslint/
│ ├ prettier/
│ ├ typescript/
│ ├ commitlint/
│ └ lint-staged/
│
├ tooling/
│ ├ scripts/
│ └ generators/
│
├ .husky/
├ docs/
├ .github/
│ └ workflows/
│
├ package.json
├ pnpm-workspace.yaml
├ turbo.json / nx.json
└ README.md
```

---

# 3. Configs Folder (Centralized Configuration Domain)

## Why?

✅ Single source of truth
✅ Easy upgrade lint / prettier / tsconfig
✅ Versionable config
✅ Reusable across repos (future)

---

## configs/typescript/

### base.json

```
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "ESNext",
    "moduleResolution": "Bundler",
    "strict": true,
    "skipLibCheck": true,
    "resolveJsonModule": true,
    "esModuleInterop": true
  }
}
```

---

## configs/prettier/

```
prettier.config.cjs
```

```
module.exports = {
  semi: false,
  singleQuote: true,
  trailingComma: "all",
  printWidth: 90
}
```

---

## configs/eslint/

```
base.js
react.js
node.js
```

---

# 4. Workspace Configuration

## pnpm-workspace.yaml

```
packages:
  - apps/*
  - packages/*
  - configs/*
```

---

# 5. Database As Package (Important Revision)

## Philosophy

DB is infrastructure → should be reusable → should be versioned → should be testable independently.

---

## packages/db Structure

```
packages/db/
├ src/
│ ├ client.ts
│ ├ schema/
│ ├ repositories/
│ ├ migrations/
│ ├ seed/
│ └ index.ts
│
├ prisma/ OR drizzle/ OR kysely/
├ package.json
└ tsconfig.json
```

---

## Why DB in packages?

### Reusability

Multiple apps share single DB layer.

### Consistency

Schema and migrations centralized.

### Testability

Can test DB logic without running full API.

### Future Scaling

Easy split into:

* read-model package
* write-model package
* event store package

---

# 6. DB Package Example Patterns

---

## client.ts

Abstract runtime differences:

```
export const runtime = process.env.RUNTIME ?? "node"
```

Then runtime adapter if needed later.

---

## repositories/

Domain-based access:

```
user.repository.ts
invoice.repository.ts
tenant.repository.ts
```

---

# 7. Shared Types Strategy

## packages/types

```
packages/types/
├ src/
│ ├ domain/
│ ├ dto/
│ ├ api/
│ └ index.ts
```

Rule:

* No runtime dependency
* Pure TS only

---

# 8. Validation Package

```
packages/validation/
```

Should mirror types domain.

Example:

```
user.schema.ts
invoice.schema.ts
```

---

# 9. Apps Layer Responsibility

Apps should be thin:

Apps = orchestration + delivery layer
Packages = business logic + infra + contracts

---

# 10. Runtime Agnostic Execution

Each app decides runtime.

Example:

```
dev:node
dev:bun
build
start
```

DB package must not assume runtime-specific APIs unless adapter pattern used.

---

# 11. Husky Setup (Centralized)

```
.husky/
```

### pre-commit

```
pnpm lint
pnpm format
pnpm typecheck
```

---

# 12. Commit Standard

configs/commitlint

---

# 13. Monorepo Orchestration

Turbo / Nx both valid.

---

# 14. Environment Strategy

Each app:

```
apps/api/.env
apps/web/.env
```

DB package must accept env injection — never load env internally.

---

# 15. Advanced DB Patterns (Recommended)

## Option A — ORM Inside DB Package

Good for:

* Small / medium team
* Fast dev

## Option B — Query Builder Only

Good for:

* Large scale
* Multi runtime
* Fine performance control

---

# 16. Future Upgrade Path

You can split DB later into:

```
packages/
  db-core
  db-read
  db-write
  db-events
```

---

# 17. Developer Experience

Recommended:

VSCode workspace config
Path alias sync with tsconfig
Auto import shared types

---

# 18. CI Pipeline

Steps:

1. Install
2. Typecheck
3. Lint
4. Test
5. Build
6. Cache

---

# 19. Enterprise Add-ons Ready

Easy to plug later:

* OpenAPI shared contract
* Event schema registry
* Shared logger package
* Feature flag package
* Observability package

---

# 20. Golden Rules

### Apps must NOT:

❌ Define shared types
❌ Define DB schema
❌ Define validation

---

### Packages must:

✅ Be reusable
✅ Be runtime-light
✅ Be framework independent

---

# 21. Starter Checklist

* Husky working
* Central configs imported
* DB package connected
* Types shared successfully
* Validation shared successfully
* At least 1 app booted
* CI green

---
