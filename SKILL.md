---
name: vite-self-host-migration
description: >-
  Migrates web apps (especially JavaScript web apps) to Vite or TanStack Start, adds production
  Docker images for self-hosting, and optionally wires Nitro for Node server output.
  Use when the user wants to leave Next.js for Vite, self-host on Docker or Coolify,
  add API routes without Next, convert App Router projects to TanStack Start, or
  optimize builds for a private server.
---

# Vite self-host migration

## Goal

Move the app to a **Vite-centric** deploy target, pick **SPA vs TanStack Start** correctly, and ship a **Dockerfile** that runs on generic hosts (including Coolify).

## Before starting

1. Read [references/next-inventory.md](references/next-inventory.md) and classify the project (server needs, Next features in use).
2. Choose a target using [references/stack-choice.md](references/stack-choice.md).
3. For long migrations, create `mistakes.md` in the project root (errors, fixes, lessons) and append as you go—same pattern as other migration skills.

## Workflow

### 1. Inventory

- Scan for `app/`, `pages/`, `middleware.ts`, `route.ts` / `route.js` handlers, `next/image`, `next/font`, `getServerSideProps`, RSC usage, and `NEXT_PUBLIC_*` vs server-only env.
- Report findings before rewriting large areas of the tree.

### 2. Pick stack

- **Mostly client-side / static**: Vite SPA (or static export) — see stack-choice doc.
- **API routes, server actions, SSR/loaders, auth on the server**: **TanStack Start** — follow [references/migration-patterns.md](references/migration-patterns.md) and TanStack’s official migrate guide.

### 3. Implement migration

- Apply mappings and phase boundaries from [references/migration-patterns.md](references/migration-patterns.md).
- **Validation gate**: after each phase, run the project `build` (and `start` or `preview` when relevant). Do not proceed on a failing build; note fixes in `mistakes.md` if present.

### 4. Docker (and optional Coolify)

- Add a Dockerfile per [references/docker-coolify.md](references/docker-coolify.md) (Node `.output` for TanStack+Nitro, or nginx for static SPA).
- Expose the port the app listens on (commonly **3000**). Align **health checks** with that port if the platform requires them.

## Quick links

- TanStack: [Migrate from Next.js](https://tanstack.com/start/latest/docs/framework/react/migrate-from-next-js)
- Coolify: [Coolify documentation](https://coolify.io/docs/)

## Additional resources

- [references/stack-choice.md](references/stack-choice.md) — Vite vs TanStack Start vs Nitro
- [references/next-inventory.md](references/next-inventory.md) — Next.js detection checklist
- [references/migration-patterns.md](references/migration-patterns.md) — Phases, mappings, validation
- [references/docker-coolify.md](references/docker-coolify.md) — Docker recipes and Coolify notes
