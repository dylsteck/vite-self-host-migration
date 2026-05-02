# Docker and Coolify

## TanStack Start + Nitro (Node server, `.output`)

Multi-stage builds keep images small: install deps + build in a builder stage, copy only runtime artifacts into the final image.

**Typical runtime command**: `node .output/server/index.mjs`  
**Typical port**: `3000` (expose what your server actually listens on).

### Example A — Bun install, Node runtime

From real-world usage (see casterscan):

```dockerfile
FROM oven/bun:1.3.4 AS builder
WORKDIR /app
COPY package.json bun.lock ./
RUN bun install --frozen-lockfile
COPY . .
RUN bun run build

FROM node:22-alpine
WORKDIR /app
COPY --from=builder /app/.output ./.output
COPY --from=builder /app/node_modules/ws /app/.output/server/node_modules/ws
EXPOSE 3000
CMD ["node", ".output/server/index.mjs"]
```

- The extra **`ws`** copy fixes **runtime resolution** for that dependency in the trimmed output (adjust if your build does not need it).
- Swap **`oven/bun`** / **`bun`** for **Node + npm/pnpm** if the project standardizes on Node for installs.

### Example B — Deps in Bun, build via Node `vite build`

From real-world usage (see dylsteck):

```dockerfile
FROM oven/bun:1.3.4 AS deps
WORKDIR /app
COPY package.json bun.lock* package-lock.json* ./
RUN if [ -f bun.lock ]; then bun install --frozen-lockfile; else bun install; fi

FROM node:22-alpine AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .
RUN node node_modules/vite/bin/vite.js build

FROM node:22-alpine
WORKDIR /app
COPY --from=builder /app/.output ./.output
# If the server reads source files at runtime (e.g. fs.readFileSync under /app), copy those paths too:
COPY --from=builder /app/app ./app
EXPOSE 3000
CMD ["node", ".output/server/index.mjs"]
```

Audit **`grep` / `readFile` / `import.meta.glob`** for paths expected at runtime; mirror them with **`COPY`** lines in the final stage.

### Health checks

- HTTP health: hit `/` or a dedicated `/health` route if you add one.
- Coolify and other orchestrators often need the **same port** as `EXPOSE` and `CMD`.

## Static Vite SPA (`dist/`)

If the final app is **static files only**:

```dockerfile
FROM node:22-alpine AS builder
WORKDIR /app
COPY package.json package-lock.json* ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
# Add a small nginx.conf for SPA fallback: try_files $uri /index.html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

Add `nginx.conf` with `try_files` so client-side routing works on refresh.

## Coolify

Coolify runs **Dockerfiles** (or compose) on your machine—optimize for:

- **Fast rebuilds**: multi-stage cache layers; order Dockerfile so dependency install caches when lockfiles unchanged.
- **Port**: map the service port to what Coolify expects; read [Coolify docs](https://coolify.io/docs/) for health checks and environment variables.
- **Volumes**: if the app writes uploads or reads mutable files, mount persistent storage—pure `.output` Node images usually do not need it unless your app reads bundled **markdown/data from disk** (then ship those files via `COPY` or a volume).

You do not need Coolify-specific files in the repo: a solid **Dockerfile** is the portable contract.
