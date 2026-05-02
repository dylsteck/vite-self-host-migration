# Stack choice: Vite SPA vs TanStack Start vs Nitro

## Vite-only (SPA or static site)

Use when:

- No **first-party HTTP API** in the Next app (or APIs will move to a separate backend).
- No hard requirement for **per-request SSR** from the same Node process; SEO is handled by static generation, a separate service, or is not critical.
- **Middleware** can be dropped, reimplemented at the edge/reverse proxy, or replaced with client-only checks.

Typical shape: `vite` + `@vitejs/plugin-react` + a client router (e.g. React Router) if needed; `vite build` → `dist/` served by nginx/Caddy or any static host.

## TanStack Start (full-stack on Vite)

Use when:

- App uses **Next API routes**, **Route Handlers**, or **server actions** you still need after migration.
- You want **loaders**, **server functions**, and file-based routes in one Vite project.
- You need a **documented** path from Next (see [Migrate from Next.js](https://tanstack.com/start/latest/docs/framework/react/migrate-from-next-js)).

TanStack Start is the default **full-stack** answer in this skill; follow its current docs for router layout, `createServerFn`, and `server.handlers` on routes.

## Nitro (`nitro/vite`) alongside TanStack Start

**Nitro is optional** but useful when you want a **stable Node deployment artifact** (e.g. `.output/server/index.mjs`) similar to other Nitro-based stacks.

Pattern (illustrative—verify versions in project docs):

```ts
import { defineConfig } from "vite";
import { tanstackStart } from "@tanstack/react-start/plugin/vite";
import { nitro } from "nitro/vite";
import viteReact from "@vitejs/plugin-react";

export default defineConfig({
  plugins: [
    tanstackStart({
      srcDirectory: "app",
      router: { routesDirectory: "routes" },
    }),
    nitro({ preset: "node-server" }),
    viteReact(),
  ],
});
```

- `preset: "node-server"` targets a **Node** process—matches multi-stage Docker images that run `node .output/server/index.mjs`.
- Plugin order and options can change across versions; always cross-check the **Nitro** and **TanStack Start** docs for the versions pinned in `package.json`.

## Anti-pattern

Do not default to "plain Vite SPA" if the app relies on **server-only secrets**, **sessions in middleware**, or **many API routes** unless you have an explicit plan to extract those to another service.
