# System architecture

This document explains **how PulseGuard AI will be built** in a way that is realistic for a small beginner team.

It agrees with:

- [PROJECT_OVERVIEW.md](./PROJECT_OVERVIEW.md) — product purpose  
- [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md) — what we build first  
- [DATABASE_DESIGN.md](./DATABASE_DESIGN.md) — data model  
- [FOLDER_STRUCTURE.md](./FOLDER_STRUCTURE.md) — code layout  
- Existing stack: **Next.js (App Router) + TypeScript + Tailwind**

First-release scope comes from [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md) and [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md). This document describes that scope. Team workspaces, invitations, roles, email notifications, Slack notifications, stored incident records, and root-cause analysis are later work and are not part of the current architecture.

---

## Architecture in plain language

Think of PulseGuard AI as **one web application** with four jobs:

1. **Show screens** (dashboard, forms, history)
2. **Handle requests** (save projects, start checks, call AI)
3. **Store data** (users, projects, endpoints, results)
4. **Talk to the outside world** (checked APIs + AI provider)

We will **not** start with many microservices. One Next.js app keeps deployment and learning simpler.

```text
                    ┌──────────────────────────┐
                    │        Browser           │
                    │  (React pages / UI)      │
                    └────────────┬─────────────┘
                                 │ HTTPS
                    ┌────────────▼─────────────┐
                    │     Next.js application  │
                    │  Frontend + API routes   │
                    │  + domain services       │
                    └──────┬─────────┬─────────┘
                           │         │
              ┌────────────▼──┐   ┌──▼─────────────┐
              │  PostgreSQL   │   │ External world │
              │  (database)   │   │ - Target APIs  │
              └───────────────┘   │ - AI Gateway   │
                                  │ - Auth provider│
                                  └────────────────┘
```

---

## Overall system architecture

### Chosen style: modular monolith

| Term | Meaning |
| ---- | ------- |
| Monolith | One deployable app (our Next.js project) |
| Modular | Code is organized into clear folders/services so it stays understandable |

This matches our `src/` layout:

| Folder | Role |
| ------ | ---- |
| `src/app/` | Pages (UI) and `api/` route handlers |
| `src/components/` | Reusable UI pieces |
| `src/services/` | Business logic (monitor, scan, recommend) |
| `src/lib/` | Small helpers (validation, date formatting, SSRF checks) |
| `src/types/` | Shared TypeScript types |
| `src/config/` | Non-secret constants |
| `src/hooks/` | Client-side React hooks when needed |

---

## Frontend responsibilities

The frontend is what users see and click.

**Owns:**

- Login / signup screens
- Project and endpoint forms
- Dashboard and detail pages
- Showing status, history, findings, and AI recommendations
- Calling our own backend routes (not calling the database directly from the browser)

**Does not own:**

- Database passwords or AI API keys
- Direct writes to PostgreSQL
- Trusting the browser alone for security checks (authorization always happens on the server)

**Beginner tip:** Prefer Server Components for data loading. Use Client Components only when you need interactivity (buttons, forms with live state).

---

## Backend responsibilities

In Next.js, the “backend” lives mainly in:

- `src/app/api/...` route handlers
- Server Actions (optional later)
- `src/services/...` functions called by those routes

**Owns:**

- Authentication session checks
- Authorization (“does this user own this project?”)
- Creating/updating/deleting projects and endpoints
- Running outbound health checks and security checks
- Saving results to the database
- Calling the AI provider and storing recommendations
- Scheduled jobs (cron endpoint that runs due checks)

**Does not own:**

- Fancy UI styling (that’s frontend/components)
- Long-running multi-hour jobs in MVP (keep checks short with timeouts)

---

## Database responsibilities

We plan to use **PostgreSQL** (a reliable relational database). Connection string: `DATABASE_URL` (see `.env.example`).

**Owns:**

- Persistent storage of users/sessions (depending on auth setup), projects, endpoints, check results, security findings, recommendations
- Relationships between those records
- Basic query performance with indexes on foreign keys and timestamps

**Does not own:**

- Business rules (those stay in `src/services`)
- Rendering HTML

Details: [DATABASE_DESIGN.md](./DATABASE_DESIGN.md).

---

## AI integration

### Goal

Turn structured monitoring/security data into **readable recommendations**.

### MVP flow

```text
User clicks "Generate recommendations"
        │
        ▼
Backend loads recent check results + findings
        │
        ▼
Backend builds a prompt (no secrets)
        │
        ▼
AI Gateway / model returns text + structured advice
        │
        ▼
Backend saves recommendation rows
        │
        ▼
UI displays them
```

### Design choices (keep it simple)

| Choice | Reason |
| ------ | ------ |
| One provider path via AI Gateway | Easier keys, switching models later |
| Manual trigger first | Controls cost and debugging |
| Store results in DB | Users can re-read advice without re-calling AI |
| Server-only API key | Browser never sees `AI_GATEWAY_API_KEY` |

Env vars (planned): `AI_GATEWAY_API_KEY`, `AI_MODEL`.

---

## External API communication

PulseGuard talks to two kinds of external HTTP targets:

### 1. Customer APIs (monitoring targets)

- Our server sends a request (MVP: mainly `GET`) to the user’s registered URL
- Both `http://` and `https://` URLs may be registered and probed. HTTPS remains supported. Plain HTTP is stored, checked, and reported as an insecure-connection finding
- We measure status code, latency, and errors
- We apply **timeouts** and **SSRF protections** (only `http` and `https`, and no private or link-local addresses)

```text
PulseGuard server ──HTTP GET──► https://api.example.com/health
                 ◄── status + body headers ──
```

### 2. AI provider

- Our server sends a prompt/payload to the AI gateway
- Response is parsed and saved

### 3. Auth provider (if managed auth is chosen)

- Login redirects / session validation handled by the auth library

---

## Scheduled monitoring (cron)

For recurring checks:

```text
Vercel Cron (or similar) ──GET/POST──► /api/cron/run-checks
                                         │
                                         ▼
                                   Find due endpoints
                                         │
                                         ▼
                                   Run checks + save results
```

Protect the cron route with a secret header/token so strangers cannot trigger mass scanning.

---

## Layered request example

Example: user opens a project page.

1. Browser requests `/projects/123`
2. Next.js server checks session (logged in?)
3. Service loads project `123` **only if** `user_id` matches
4. Database returns project + endpoints + latest statuses
5. Server renders the page (or returns JSON for a client fetch)

---

## Deployment shape (MVP)

| Piece | Suggested choice |
| ----- | ---------------- |
| App hosting | Vercel |
| Database | Hosted PostgreSQL (e.g. Marketplace/Neon) |
| Auth | Mature library / managed auth |
| AI | Vercel AI Gateway (or compatible provider) |
| Cron | Vercel Cron |

Local development uses `npm run dev` + `.env.local`.

---

## Technical risks & architecture mitigations

| Risk | Architecture mitigation |
| ---- | ----------------------- |
| SSRF via monitor URLs | Validate URL; allow `http` and `https`; block other schemes and private/link-local IPs; timeout. Report `http` as a security finding instead of rejecting it at registration |
| Leaked secrets | Env vars only; never expose server keys to client bundles |
| Cross-user data access | Every query filtered by authenticated user ownership |
| AI prompt injection / sensitive data leakage | Don’t send API tokens or raw secrets to the model |
| Over-complex distributed design | Stay on modular monolith until MVP is stable |
| Check storms | Limit concurrency; schedule intervals; backoff on repeated failure |

---

## Security considerations (architecture-level)

1. **Trust boundaries:** Browser is untrusted. Server enforces authz.
2. **Least privilege:** DB user should only access the PulseGuard database.
3. **Outbound controls:** Monitoring is an outbound HTTP client — treat target URLs as hostile input.
4. **Secret handling:** `DATABASE_URL`, `AUTH_SECRET`, `AI_GATEWAY_API_KEY`, cron secrets stay server-side.
5. **Dependency hygiene:** Prefer well-known libraries for auth and DB access.

---

## What we are deliberately not building yet

- Separate microservice per feature
- Message queues / event buses
- Multi-region probe network
- Real-time streaming dashboards
- Team workspaces, invitations, and role management
- Email, Slack, and other outbound notifications
- A stored `failing` status, incident table, or root-cause analysis

The dashboard derives “failing” from recent `check_results`. `endpoints.last_status` stays a cache of `up`, `down`, or `unknown`, and it is updated in the same operation as every new check result.

These later items can be reconsidered after MVP if real usage demands them.

---

## Related documents

- [DATABASE_DESIGN.md](./DATABASE_DESIGN.md)
- [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md)
- [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md)
- [ARCHITECTURE.md](./ARCHITECTURE.md) — shorter earlier summary (still valid; this file is the detailed plan)
