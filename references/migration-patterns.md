# Migration patterns and phases

Official TanStack reference: [Migrate from Next.js](https://tanstack.com/start/latest/docs/framework/react/migrate-from-next-js). Prefer linking to current docs over copying full APIs here.

## Phase 0: Baseline

- Record current `next dev` / `next build` / `next start` behavior and critical routes.
- Add or run tests if they exist; note gaps.

## Phase 1: Dependencies and tooling

- Remove Next packages; add Vite + target stack (`@vitejs/plugin-react`, TanStack Start packages, etc.).
- Replace scripts: `dev` → `vite dev` (or Start’s documented command), `build` → `vite build`, `start` → `node .output/server/index.mjs` (or static server) as appropriate.
- **Gate**: `build` succeeds on a minimal shell (single route or placeholder).

## Phase 2: Routing shell

- Reproduce route URLs the product relies on (including dynamic segments).
- Map Next dynamic segments (`[id]`) to TanStack Router file routes (see TanStack docs for current file naming).
- **Gate**: navigate primary user journeys without 404s.

## Phase 3: Data and server behavior

Per TanStack’s migrate guide (patterns, not exhaustive):

- **API route handlers** → `createFileRoute` with `server.handlers` (`GET`, `POST`, etc.) returning `Response` / `Response.json`.
- **Server Actions** → `createServerFn` from `@tanstack/react-start`.
- **`getServerSideProps`-style data** → route **`loader`** with `Route.useLoaderData()` in the component.

- **Gate**: critical APIs and pages return correct data; run build again.

## Phase 4: Client-only concerns

- Replace `next/image` and `next/font` per inventory.
- Replace `next/link` and navigation hooks with the target router.
- Ensure `"use client"` boundaries become plain client modules (no Next server/client split—TanStack uses its own server function and loader boundaries).

## Phase 5: Env and config

- Map `NEXT_PUBLIC_*` to the bundler’s exposed env rules (Vite: `VITE_*` for client; keep secrets server-side only in Start server functions).
- Port `next.config` rewrites/headers to **Vite** (`server.proxy`), **TanStack Start** server routes, or **reverse proxy** (nginx/Caddy/Coolify).

## Sharp edges (expect work)

- **Middleware auth** often becomes **server function checks**, **route beforeLoad**, or **proxy-level auth**.
- **RSC data waterfalls** become explicit loaders and client/server splits in Start—plan file-by-file.
- **ISR**: reproduce with explicit cache headers, background jobs, or static rebuild pipelines—not a 1:1 toggle.

## Optional: mistakes log

For large migrations, maintain `mistakes.md` at repo root:

```markdown
# Migration mistakes & learnings

## Errors

## Lessons learned
```

Append each non-obvious fix so the next pass is faster.
