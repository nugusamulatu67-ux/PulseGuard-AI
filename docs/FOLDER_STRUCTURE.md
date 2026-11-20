# Folder structure

This project uses a `src/`-based Next.js App Router layout so application code stays separate from config and documentation.

## Root

| Path | Purpose |
| ---- | ------- |
| `docs/` | Team documentation (architecture, conventions, roadmap) |
| `public/` | Static files (images, icons, favicons) served at the site root |
| `src/` | All application source code |
| `.env.example` | Safe template for required environment variables |
| `.gitignore` | Files and folders excluded from version control |
| `eslint.config.mjs` | ESLint configuration |
| `next.config.ts` | Next.js configuration |
| `package.json` | Dependencies and npm scripts |
| `tsconfig.json` | TypeScript compiler options and path aliases |

## `src/app/`

Next.js App Router entry point.

| Path | Purpose |
| ---- | ------- |
| `layout.tsx` | Root layout (fonts, metadata, global shell) |
| `page.tsx` | Home route (`/`) |
| `globals.css` | Global styles and Tailwind entry |
| `api/` | Route handlers for HTTP APIs (e.g. `/api/...`) |

Add nested route folders as features are built (for example `dashboard/`, `settings/`).

## `src/components/`

Reusable React components.

| Path | Purpose |
| ---- | ------- |
| `ui/` | Small, generic UI primitives (buttons, inputs, badges) |
| `layout/` | Structural pieces (header, sidebar, page shells) |

Feature-specific components can be added as sibling folders when needed (e.g. `components/monitoring/`).

## `src/lib/`

Framework-agnostic helpers and shared logic that is not UI.

| Path | Purpose |
| ---- | ------- |
| `utils/` | Pure utility functions (formatting, validation helpers, etc.) |

## `src/services/`

Clients and orchestration for external systems and domain operations (database access, AI calls, scanners, monitors). Keep UI out of this folder.

## `src/hooks/`

Shared React hooks used across multiple components or pages.

## `src/types/`

Shared TypeScript types and interfaces. Prefer exporting named types from here instead of duplicating shapes across files.

## `src/config/`

Application constants, feature flags, and static configuration that is not secret.

## Conventions

- Use the `@/` import alias for anything under `src/` (configured in `tsconfig.json`).
- Prefer server components by default; add `"use client"` only when browser APIs or interactivity require it.
- Do not place secrets in `src/config/` — use environment variables instead.
- Keep empty placeholder folders with `.gitkeep` until real files are added.
