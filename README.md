# vite-self-host-migration

Agent skill for migrating web apps (especially **Next.js**) to **Vite** or **TanStack Start**, adding **Docker** for self-hosting, and optionally **Nitro** for a Node `.output` server—useful for **Coolify** or any Docker host.

## What it covers

- When to choose a **Vite SPA** vs **TanStack Start**
- **Next.js inventory** (API routes, RSC, middleware, `next/image`, env, etc.)
- **Migration phases** and links to [TanStack’s Next.js migration guide](https://tanstack.com/start/latest/docs/framework/react/migrate-from-next-js)
- **Dockerfile** patterns (Node + `.output`, static nginx) and brief **Coolify** notes

See [`SKILL.md`](./SKILL.md) and [`references/`](./references/) for full instructions.

## Install (Skills CLI)

```bash
npx skills add dylsteck/vite-self-host-migration
```

## Cursor (manual)

Symlink or copy this folder into `~/.cursor/skills/vite-self-host-migration`, or keep developing from this repo path.

## Validate (optional)

```bash
npx skills-ref validate .
```
