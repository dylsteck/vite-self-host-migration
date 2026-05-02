# Next.js inventory checklist

Run through this before choosing Vite-only vs TanStack Start. Note **Yes/No** per item for the migration report.

## Project shape

- [ ] **Package manager**: npm, pnpm, yarn, bun (lockfile present?)
- [ ] **Router**: `app/` (App Router) vs `pages/` vs mixed
- [ ] **TypeScript** vs JavaScript

## Server and data

- [ ] **`app/api/**` or `pages/api/**`** — HTTP handlers to preserve
- [ ] **Route Handlers** (`route.ts` exporting `GET`/`POST`/…)
- [ ] **Server Actions** (`"use server"`)
- [ ] **`getServerSideProps` / `getStaticProps` / `getStaticPaths`**
- [ ] **React Server Components** (default server components in `app/`)
- [ ] **ISR / revalidate** (`revalidate`, `export const revalidate`)

## Edge and request lifecycle

- [ ] **`middleware.ts`** — auth, redirects, geo, rewrites
- [ ] **Draft mode**, **parallel routes**, **intercepting routes** (high complexity)

## Next-specific APIs

- [ ] **`next/image`** — replace with `<img>`, Vite-friendly image tooling, or CDN
- [ ] **`next/font`** — replace with `@fontsource/*`, CSS `@font-face`, or hosted fonts
- [ ] **`next/link`** — replace with framework router link
- [ ] **`next/head` or metadata API** — replace with TanStack/meta or static HTML head management
- [ ] **`next/navigation` hooks** — map to target router

## Config and env

- [ ] **`next.config`**: rewrites, redirects, headers, `images.domains`
- [ ] **Env**: `NEXT_PUBLIC_*` (browser) vs server-only secrets—plan renames (`VITE_*` only where appropriate for Vite client exposure rules)

## Auth and third parties

- [ ] **NextAuth / Auth.js** or other session middleware tied to Next request context
- [ ] **Vercel-specific** features (edge config, KV, etc.) — need non-Vercel substitutes

## Interpretation

- Many **Yes** in server/API/RSC/middleware → plan for **TanStack Start** (not Vite-only SPA).
- Mostly **No** there, mostly client components and static content → **Vite SPA or static** is realistic.
