# Comprehensive Frontend Blueprint

A complete guide for setting up modern, production-ready frontend applications with TypeScript, supporting multiple frameworks and best practices.

## Table of Contents

1. [Project Initialization Questions](#project-initialization-questions)
2. [Project Structure](#project-structure)
3. [Framework-Specific Setup](#framework-specific-setup)
4. [State Management](#state-management)
5. [Routing](#routing)
6. [Styling Solutions](#styling-solutions)
7. [Form Handling](#form-handling)
8. [API Integration](#api-integration)
9. [Testing Setup](#testing-setup)
10. [Build & Optimization](#build--optimization)
11. [Development Tools](#development-tools)
12. [Deployment](#deployment)

---

## Project Initialization Questions

Before starting, answer these questions to customize your setup:

### Required Questions

1. **What framework do you want to use?**
   - React
   - Vue
   - Svelte
   - Solid
   - Angular
   - Vanilla TypeScript

2. **What state management solution do you prefer?**
   
   **For React:**
   - Zustand (recommended for simplicity)
   - Redux Toolkit
   - Jotai
   - Recoil
   - Context API + useReducer
   - TanStack Query (for server state)
   - None (useState/useReducer only)

   **For Vue:**
   - Pinia (recommended)
   - Vuex
   - Composables only
   - None

   **For Svelte:**
   - Svelte Stores (built-in)
   - None

   **For Solid:**
   - Solid Signals (built-in)
   - None

   **For Angular:**
   - NgRx
   - Akita
   - Services + RxJS
   - Signals (Angular 16+)

3. **What styling solution do you want?**
   - Tailwind CSS
   - CSS Modules
   - Styled Components / Emotion
   - Sass/SCSS
   - Vanilla CSS
   - UnoCSS
   - Panda CSS

4. **Do you need routing?**
   - Yes (SPA with client-side routing)
   - Yes (SSR/SSG with framework routing)
   - No (single page application)

5. **What build tool do you prefer?**
   - Vite (recommended)
   - Webpack
   - Turbopack
   - esbuild
   - Rollup

6. **Do you need Server-Side Rendering (SSR)?**
   - Yes (Next.js for React, Nuxt for Vue, SvelteKit for Svelte, etc.)
   - No (Client-side only)

### Optional Questions

7. **What UI component library do you want?**
   - None (custom components)
   - shadcn/ui (React)
   - Material UI (React)
   - Ant Design (React/Vue)
   - Chakra UI (React)
   - Vuetify (Vue)
   - PrimeVue (Vue)
   - Flowbite (Multi-framework)
   - DaisyUI (Tailwind-based)

8. **What form handling library?**
   - React Hook Form (React)
   - Formik (React)
   - VeeValidate (Vue)
   - Zod (validation)
   - Yup (validation)
   - None (native forms)

9. **What API client do you prefer?**
   - Fetch API (native)
   - Axios
   - TanStack Query (React Query)
   - SWR
   - tRPC (type-safe)
   - GraphQL (Apollo/urql)

10. **Do you need authentication?**
    - Yes (JWT)
    - Yes (OAuth/Social)
    - Yes (NextAuth.js)
    - Yes (Clerk)
    - Yes (Supabase Auth)
    - No

11. **What testing approach?**
    - Vitest + Testing Library
    - Jest + Testing Library
    - Playwright (E2E)
    - Cypress (E2E)
    - All of the above

12. **Do you need internationalization (i18n)?**
    - Yes (react-i18next, vue-i18n, etc.)
    - No

13. **Project type?**
    - Part of monorepo
    - Standalone project

---

## Project Structure

### Standalone Project Structure

```
frontend/
├── public/                  # Static assets
│   ├── favicon.ico
│   └── images/
├── src/
│   ├── assets/             # Images, fonts, etc.
│   ├── components/         # Reusable components
│   │   ├── ui/            # Base UI components
│   │   ├── forms/         # Form components
│   │   └── layout/        # Layout components
│   ├── features/          # Feature-based modules
│   │   ├── auth/
│   │   ├── dashboard/
│   │   └── profile/
│   ├── hooks/             # Custom hooks (React/Vue)
│   ├── lib/               # Utility functions
│   ├── services/          # API services
│   ├── store/             # State management
│   ├── styles/            # Global styles
│   ├── types/             # TypeScript types
│   ├── config/            # App configuration
│   ├── routes/            # Route definitions
│   ├── App.tsx            # Root component
│   ├── main.tsx           # Entry point
│   └── vite-env.d.ts      # Vite types
├── tests/
│   ├── unit/
│   ├── integration/
│   └── e2e/
├── .env.example
├── .eslintrc.js
├── .prettierrc.js
├── index.html
├── package.json
├── tsconfig.json
├── vite.config.ts
└── README.md
```

### Monorepo Integration Structure

```
apps/
└── web/                    # Frontend app
    ├── public/
    ├── src/
    │   ├── components/
    │   ├── features/
    │   ├── hooks/
    │   ├── lib/
    │   ├── services/
    │   ├── store/
    │   ├── styles/
    │   ├── App.tsx
    │   └── main.tsx
    ├── package.json
    ├── tsconfig.json
    └── vite.config.ts
```

---

## Framework-Specific Setup

### React + Vite Setup

#### Installation

```bash
# Create project
pnpm create vite@latest my-app --template react-ts

# Or in monorepo
cd apps/web
pnpm init
```

#### package.json

```json
{
  "name": "my-app",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint . --ext ts,tsx",
    "type-check": "tsc --noEmit"
  },
  "dependencies": {
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

#### vite.config.ts

```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@components': path.resolve(__dirname, './src/components'),
      '@features': path.resolve(__dirname, './src/features'),
      '@hooks': path.resolve(__dirname, './src/hooks'),
      '@lib': path.resolve(__dirname, './src/lib'),
      '@services': path.resolve(__dirname, './src/services'),
      '@store': path.resolve(__dirname, './src/store'),
      '@types': path.resolve(__dirname, './src/types'),
    },
  },
  server: {
    port: 3000,
    proxy: {
      '/api': {
        target: 'http://localhost:4000',
        changeOrigin: true,
      },
    },
  },
});
```

#### tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "noEmit": true,
    "jsx": "react-jsx",
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "baseUrl": ".",
    "paths": {
      "@/*": ["./src/*"],
      "@components/*": ["./src/components/*"],
      "@features/*": ["./src/features/*"],
      "@hooks/*": ["./src/hooks/*"],
      "@lib/*": ["./src/lib/*"],
      "@services/*": ["./src/services/*"],
      "@store/*": ["./src/store/*"],
      "@types/*": ["./src/types/*"]
    }
  },
  "include": ["src"],
  "references": [{ "path": "./tsconfig.node.json" }]
}
```

### Vue + Vite Setup

#### Installation

```bash
pnpm create vite@latest my-app --template vue-ts
```

#### vite.config.ts

```typescript
import { defineConfig } from 'vite';
import vue from '@vitejs/plugin-vue';
import path from 'path';

export default defineConfig({
  plugins: [vue()],
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
  server: {
    port: 3000,
  },
});
```

### Svelte + Vite Setup

#### Installation

```bash
pnpm create vite@latest my-app --template svelte-ts
```

#### vite.config.ts

```typescript
import { defineConfig } from 'vite';
import { svelte } from '@sveltejs/vite-plugin-svelte';

export default defineConfig({
  plugins: [svelte()],
  server: {
    port: 3000,
  },
});
```

---

## State Management

### React - Zustand (Recommended)

#### Installation

```bash
pnpm add zustand
```

#### Store Setup

```typescript
// src/store/useAuthStore.ts
import { create } from 'zustand';
import { persist } from 'zustand/middleware';

interface User {
  id: string;
  email: string;
  name: string;
}

interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
  login: (user: User, token: string) => void;
  logout: () => void;
}

export const useAuthStore = create<AuthState>()(
  persist(
    (set) => ({
      user: null,
      token: null,
      isAuthenticated: false,
      login: (user, token) =>
        set({ user, token, isAuthenticated: true }),
      logout: () =>
        set({ user: null, token: null, isAuthenticated: false }),
    }),
    {
      name: 'auth-storage',
    }
  )
);
```

#### Usage

```typescript
// In component
import { useAuthStore } from '@/store/useAuthStore';

function Profile() {
  const { user, logout } = useAuthStore();

  return (
    <div>
      <h1>Welcome, {user?.name}</h1>
      <button onClick={logout}>Logout</button>
    </div>
  );
}
```

### React - Redux Toolkit

#### Installation

```bash
pnpm add @reduxjs/toolkit react-redux
```

#### Store Setup

```typescript
// src/store/index.ts
import { configureStore } from '@reduxjs/toolkit';
import authReducer from './slices/authSlice';

export const store = configureStore({
  reducer: {
    auth: authReducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

```typescript
// src/store/slices/authSlice.ts
import { createSlice, PayloadAction } from '@reduxjs/toolkit';

interface User {
  id: string;
  email: string;
  name: string;
}

interface AuthState {
  user: User | null;
  token: string | null;
  isAuthenticated: boolean;
}

const initialState: AuthState = {
  user: null,
  token: null,
  isAuthenticated: false,
};

const authSlice = createSlice({
  name: 'auth',
  initialState,
  reducers: {
    login: (state, action: PayloadAction<{ user: User; token: string }>) => {
      state.user = action.payload.user;
      state.token = action.payload.token;
      state.isAuthenticated = true;
    },
    logout: (state) => {
      state.user = null;
      state.token = null;
      state.isAuthenticated = false;
    },
  },
});

export const { login, logout } = authSlice.actions;
export default authSlice.reducer;
```

### Vue - Pinia

#### Installation

```bash
pnpm add pinia
```

#### Store Setup

```typescript
// src/stores/auth.ts
import { defineStore } from 'pinia';

interface User {
  id: string;
  email: string;
  name: string;
}

export const useAuthStore = defineStore('auth', {
  state: () => ({
    user: null as User | null,
    token: null as string | null,
  }),
  getters: {
    isAuthenticated: (state) => !!state.token,
  },
  actions: {
    login(user: User, token: string) {
      this.user = user;
      this.token = token;
    },
    logout() {
      this.user = null;
      this.token = null;
    },
  },
  persist: true,
});
```

---

## Routing

### React Router

#### Installation

```bash
pnpm add react-router-dom
```

#### Setup

```typescript
// src/routes/index.tsx
import { createBrowserRouter } from 'react-router-dom';
import { Layout } from '@/components/layout/Layout';
import { HomePage } from '@/features/home/HomePage';
import { LoginPage } from '@/features/auth/LoginPage';
import { DashboardPage } from '@/features/dashboard/DashboardPage';
import { ProtectedRoute } from '@/components/ProtectedRoute';

export const router = createBrowserRouter([
  {
    path: '/',
    element: <Layout />,
    children: [
      {
        index: true,
        element: <HomePage />,
      },
      {
        path: 'login',
        element: <LoginPage />,
      },
      {
        path: 'dashboard',
        element: (
          <ProtectedRoute>
            <DashboardPage />
          </ProtectedRoute>
        ),
      },
    ],
  },
]);
```

```typescript
// src/main.tsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { RouterProvider } from 'react-router-dom';
import { router } from './routes';
import './styles/index.css';

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>
);
```

### Vue Router

#### Installation

```bash
pnpm add vue-router
```

#### Setup

```typescript
// src/router/index.ts
import { createRouter, createWebHistory } from 'vue-router';
import HomePage from '@/views/HomePage.vue';
import LoginPage from '@/views/LoginPage.vue';

export const router = createRouter({
  history: createWebHistory(),
  routes: [
    {
      path: '/',
      name: 'home',
      component: HomePage,
    },
    {
      path: '/login',
      name: 'login',
      component: LoginPage,
    },
    {
      path: '/dashboard',
      name: 'dashboard',
      component: () => import('@/views/DashboardPage.vue'),
      meta: { requiresAuth: true },
    },
  ],
});
```

---

## Styling Solutions

### Tailwind CSS

#### Installation

```bash
pnpm add -D tailwindcss postcss autoprefixer
pnpm exec tailwindcss init -p
```

#### Configuration

```javascript
// tailwind.config.js
export default {
  content: ['./index.html', './src/**/*.{js,ts,jsx,tsx,vue,svelte}'],
  theme: {
    extend: {
      colors: {
        primary: {
          50: '#eff6ff',
          500: '#3b82f6',
          900: '#1e3a8a',
        },
      },
    },
  },
  plugins: [],
};
```

```css
/* src/styles/index.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer base {
  body {
    @apply bg-gray-50 text-gray-900;
  }
}

@layer components {
  .btn-primary {
    @apply bg-primary-500 text-white px-4 py-2 rounded hover:bg-primary-600;
  }
}
```

### CSS Modules

```typescript
// Button.module.css
.button {
  padding: 0.5rem 1rem;
  border-radius: 0.25rem;
  background-color: #3b82f6;
  color: white;
}

.button:hover {
  background-color: #2563eb;
}
```

```typescript
// Button.tsx
import styles from './Button.module.css';

export function Button({ children }: { children: React.ReactNode }) {
  return <button className={styles.button}>{children}</button>;
}
```

---

## Form Handling

### React Hook Form + Zod

#### Installation

```bash
pnpm add react-hook-form @hookform/resolvers zod
```

#### Usage

```typescript
// src/features/auth/LoginForm.tsx
import { useForm } from 'react-hook-form';
import { zodResolver } from '@hookform/resolvers/zod';
import { z } from 'zod';

const loginSchema = z.object({
  email: z.string().email('Invalid email address'),
  password: z.string().min(8, 'Password must be at least 8 characters'),
});

type LoginFormData = z.infer<typeof loginSchema>;

export function LoginForm() {
  const {
    register,
    handleSubmit,
    formState: { errors, isSubmitting },
  } = useForm<LoginFormData>({
    resolver: zodResolver(loginSchema),
  });

  const onSubmit = async (data: LoginFormData) => {
    console.log(data);
    // Handle login
  };

  return (
    <form onSubmit={handleSubmit(onSubmit)}>
      <div>
        <label htmlFor="email">Email</label>
        <input
          id="email"
          type="email"
          {...register('email')}
        />
        {errors.email && <span>{errors.email.message}</span>}
      </div>

      <div>
        <label htmlFor="password">Password</label>
        <input
          id="password"
          type="password"
          {...register('password')}
        />
        {errors.password && <span>{errors.password.message}</span>}
      </div>

      <button type="submit" disabled={isSubmitting}>
        {isSubmitting ? 'Loading...' : 'Login'}
      </button>
    </form>
  );
}
```

---

## API Integration

### TanStack Query (React Query)

#### Installation

```bash
pnpm add @tanstack/react-query
```

#### Setup

```typescript
// src/main.tsx
import { QueryClient, QueryClientProvider } from '@tanstack/react-query';

const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      staleTime: 1000 * 60 * 5, // 5 minutes
      retry: 1,
    },
  },
});

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <QueryClientProvider client={queryClient}>
      <RouterProvider router={router} />
    </QueryClientProvider>
  </React.StrictMode>
);
```

#### API Service

```typescript
// src/services/api.ts
import axios from 'axios';

const api = axios.create({
  baseURL: import.meta.env.VITE_API_URL || 'http://localhost:4000/api',
  headers: {
    'Content-Type': 'application/json',
  },
});

// Request interceptor
api.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  if (token) {
    config.headers.Authorization = `Bearer ${token}`;
  }
  return config;
});

// Response interceptor
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Handle unauthorized
      localStorage.removeItem('token');
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export { api };
```

#### Usage with React Query

```typescript
// src/features/users/hooks/useUsers.ts
import { useQuery } from '@tanstack/react-query';
import { api } from '@/services/api';

interface User {
  id: string;
  name: string;
  email: string;
}

export function useUsers() {
  return useQuery({
    queryKey: ['users'],
    queryFn: async () => {
      const { data } = await api.get<User[]>('/users');
      return data;
    },
  });
}
```

```typescript
// In component
import { useUsers } from './hooks/useUsers';

export function UserList() {
  const { data: users, isLoading, error } = useUsers();

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error loading users</div>;

  return (
    <ul>
      {users?.map((user) => (
        <li key={user.id}>{user.name}</li>
      ))}
    </ul>
  );
}
```

---

## Testing Setup

### Vitest + Testing Library

#### Installation

```bash
pnpm add -D vitest @testing-library/react @testing-library/jest-dom @testing-library/user-event jsdom
```

#### Configuration

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './tests/setup.ts',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html'],
    },
  },
  resolve: {
    alias: {
      '@': path.resolve(__dirname, './src'),
    },
  },
});
```

```typescript
// tests/setup.ts
import '@testing-library/jest-dom';
```

#### Example Test

```typescript
// src/components/Button.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { Button } from './Button';

describe('Button', () => {
  it('renders button with text', () => {
    render(<Button>Click me</Button>);
    expect(screen.getByRole('button')).toHaveTextContent('Click me');
  });

  it('calls onClick when clicked', async () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click me</Button>);
    
    await userEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

---

## Build & Optimization

### Environment Variables

```bash
# .env.example
VITE_API_URL=http://localhost:4000/api
VITE_APP_NAME=My App
VITE_ENABLE_ANALYTICS=false
```

```typescript
// src/config/env.ts
export const env = {
  apiUrl: import.meta.env.VITE_API_URL,
  appName: import.meta.env.VITE_APP_NAME,
  enableAnalytics: import.meta.env.VITE_ENABLE_ANALYTICS === 'true',
} as const;
```

### Code Splitting

```typescript
// Lazy load routes
const DashboardPage = lazy(() => import('@/features/dashboard/DashboardPage'));

// In router
{
  path: 'dashboard',
  element: (
    <Suspense fallback={<LoadingSpinner />}>
      <DashboardPage />
    </Suspense>
  ),
}
```

### Build Configuration

```typescript
// vite.config.ts
export default defineConfig({
  build: {
    target: 'esnext',
    minify: 'terser',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom', 'react-router-dom'],
          ui: ['@radix-ui/react-dialog', '@radix-ui/react-dropdown-menu'],
        },
      },
    },
  },
});
```

---

## Development Tools

### ESLint Configuration

```javascript
// .eslintrc.cjs
module.exports = {
  extends: [
    'eslint:recommended',
    'plugin:@typescript-eslint/recommended',
    'plugin:react-hooks/recommended',
    'plugin:react/recommended',
    'prettier',
  ],
  parser: '@typescript-eslint/parser',
  plugins: ['react-refresh'],
  rules: {
    'react-refresh/only-export-components': 'warn',
    'react/react-in-jsx-scope': 'off',
  },
};
```

### VS Code Settings

```json
// .vscode/settings.json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "typescript.tsdk": "node_modules/typescript/lib",
  "typescript.enablePromptUseWorkspaceTsdk": true
}
```

---

## Deployment

### Vercel

```json
// vercel.json
{
  "buildCommand": "pnpm build",
  "outputDirectory": "dist",
  "framework": "vite",
  "env": {
    "VITE_API_URL": "@api-url"
  }
}
```

### Netlify

```toml
# netlify.toml
[build]
  command = "pnpm build"
  publish = "dist"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

### Docker

```dockerfile
# Dockerfile
FROM node:20-alpine AS builder
WORKDIR /app
COPY package.json pnpm-lock.yaml ./
RUN corepack enable pnpm && pnpm install --frozen-lockfile
COPY . .
RUN pnpm build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

---

## Complete Setup Checklist

- [ ] Answer initialization questions
- [ ] Create project structure
- [ ] Setup framework and build tool
- [ ] Configure TypeScript
- [ ] Setup state management
- [ ] Configure routing
- [ ] Setup styling solution
- [ ] Configure form handling
- [ ] Setup API integration
- [ ] Configure testing
- [ ] Setup ESLint and Prettier
- [ ] Configure environment variables
- [ ] Setup CI/CD
- [ ] Configure deployment

---

## Quick Start Scripts

```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest run --coverage",
    "lint": "eslint . --ext ts,tsx",
    "lint:fix": "eslint . --ext ts,tsx --fix",
    "format": "prettier --write \"src/**/*.{ts,tsx,css}\"",
    "type-check": "tsc --noEmit"
  }
}
```

---

## Conclusion

This blueprint provides a comprehensive foundation for modern frontend development. Customize based on your specific needs and team preferences.
