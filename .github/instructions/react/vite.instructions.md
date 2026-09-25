# Vite Build Tool Instructions

**applyTo:** `frontend/**`, `vite.config.ts`, `tsconfig.json`, `.env*`

## Overview

**Vite** is the build tool and development server for the React + TypeScript frontend. Vite provides fast Hot Module Replacement (HMR), optimized production builds, and proxy support for local development.

## Configuration

### vite.config.ts

- Use **React plugin** for JSX support and HMR.
- Enable **strict TypeScript** checking during build.
- Configure the development proxy to forward `/api` and `/socket.io` to the FastAPI backend.

Example:
```typescript
import { defineConfig } from 'vite';
import react from '@vitejs/plugin-react';

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8000',
        changeOrigin: true,
      },
      '/socket.io': {
        target: 'http://localhost:8000',
        ws: true,
      },
    },
  },
  build: {
    outDir: 'dist',
    sourcemap: false, // set to true for debugging in production
  },
});
```

### tsconfig.json

- Set `"strict": true` for all TypeScript checks.
- Target ES2020+ (modern JavaScript).
- Enable JSX with React 17+ automatic runtime:
  ```json
  {
    "compilerOptions": {
      "target": "ES2020",
      "jsx": "react-jsx",
      "strict": true,
      "esModuleInterop": true,
      "skipLibCheck": true,
      "forceConsistentCasingInFileNames": true,
      "resolveJsonModule": true,
      "moduleResolution": "node"
    },
    "include": ["src"],
    "exclude": ["node_modules", "dist"]
  }
  ```

## Environment variables

### Use `.env` files sparingly

- **`.env`** — shared across all environments (dev, staging, production).
- **`.env.local`** — local overrides (not committed).
- **`.env.production`** — production-only variables (committed if safe).

### `VITE_*` prefix requirement

Vite only exposes variables prefixed with `VITE_` to the client bundle:

```bash
# Good — exposed to browser
VITE_API_BASE_URL=http://localhost:8000

# Bad — not exposed (Vite ignores it)
API_BASE_URL=http://localhost:8000
```

### Security: never store secrets in VITE_ variables

**DO NOT** put API keys, passwords, tokens, or any secrets in `.env` files or `VITE_*` variables. The browser can see them in the bundle and network requests. Use backend-only environment variables or server-side proxy endpoints.

Example `.env` for local development:
```bash
# Safe — public configuration
VITE_API_BASE_URL=http://localhost:8000
VITE_SOCKET_IO_URL=http://localhost:8000
VITE_APP_NAME=Caro Online
VITE_DEFAULT_LOCALE=vi
```

Access in code:
```typescript
const API_BASE = import.meta.env.VITE_API_BASE_URL || 'http://localhost:8000';
```

## Development server

### Running locally

```bash
npm run dev
# or
yarn dev
```

This starts Vite on `http://localhost:5173` (or next available port) with HMR enabled.

### Hot Module Replacement (HMR)

- Vite automatically reloads changes to `.tsx` and `.ts` files.
- Component state may be preserved across HMR updates (depending on React Fast Refresh).
- If HMR fails, manually refresh the browser.

### Proxy for `/api` and `/socket.io`

The `vite.config.ts` proxy forwards requests to the FastAPI backend on port 8000:

- `http://localhost:5173/api/*` → `http://localhost:8000/api/*`
- `http://localhost:5173/socket.io/*` → `http://localhost:8000/socket.io/*` (WebSocket upgrade)

This avoids CORS issues during local development.

## Production build

### Building

```bash
npm run build
# or
yarn build
```

Vite outputs optimized, minified assets to the `dist/` directory:
- `index.html` — entry point
- `assets/` — JavaScript and CSS chunks (with hash-based names)
- Source maps (if enabled in config)

### Serving static files from FastAPI

For a single-port deployment, configure FastAPI to serve Vite's static build:

```python
from fastapi.staticfiles import StaticFiles

app.mount('/static', StaticFiles(directory='frontend/dist/assets', html=False), name='static')
app.mount('/', StaticFiles(directory='frontend/dist', html=True), name='root')
```

This serves:
- `/api/*` and `/socket.io/*` → FastAPI handlers
- `/*` → `frontend/dist/index.html` (SPA fallback)

### Preview before deploy

```bash
npm run preview
# or
yarn preview
```

Starts a local server to test the production build locally.

## Dependencies and package.json

### Frontend-only dependencies

All frontend dependencies go in the frontend `package.json`:
- React, ReactDOM
- TypeScript
- Vite and plugins
- Testing libraries (Vitest, React Testing Library)
- Socket.IO client

### Scripts

Typical frontend `package.json` scripts:
```json
{
  "scripts": {
    "dev": "vite",
    "build": "tsc && vite build",
    "preview": "vite preview",
    "lint": "eslint src --ext ts,tsx",
    "test": "vitest"
  },
  "devDependencies": {
    "@vitejs/plugin-react": "^4.0.0",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "typescript": "^5.0.0",
    "vite": "^5.0.0",
    "vitest": "^1.0.0"
  },
  "dependencies": {
    "socket.io-client": "^4.x.x"
  }
}
```

### Node version

- Node 20+ recommended (or as specified in `.envrc`).
- Use `nvm` or `asdf` to match the workspace Node version.

## Code splitting and lazy loading

- Vite automatically code-splits dynamic imports:
  ```typescript
  const BoardView = React.lazy(() => import('./views/BoardView'));
  ```
- This reduces the initial bundle size.
- Wrap lazy components with `Suspense` for loading states.

## ESLint and formatting

- Use **ESLint** for linting React and TypeScript code.
- Use **Prettier** for consistent formatting.
- Run linting before committing:
  ```bash
  npm run lint
  npm run format
  ```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| HMR not working | Restart Vite dev server or refresh browser |
| Proxy errors for `/api` or `/socket.io` | Ensure FastAPI is running on `http://localhost:8000` |
| TypeScript errors in build | Run `tsc --noEmit` to type-check before build |
| Stale cache in browser | Use browser dev tools to clear cache or do hard refresh (Ctrl+Shift+R) |
| CORS errors on `/api` | Confirm Vite proxy is configured in `vite.config.ts` |
