# Database design

This document proposes the **MVP database schema** for PulseGuard AI.

- Database: **PostgreSQL**
- Style: simple relational tables (easy for beginners to understand)
- Aligns with [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md) and [SYSTEM_ARCHITECTURE.md](./SYSTEM_ARCHITECTURE.md)

**Important:** This is a design proposal. Tables are **not created in code yet**.

---

## Design principles (beginner-friendly)

1. **One fact in one place** — avoid duplicating the same data in many tables.
2. **Clear ownership** — almost everything ties back to a `user` through a `project`.
3. **History is append-only** — check results are inserted, not overwritten (so we can see trends).
4. **Use IDs** — every row has a unique `id` (UUID recommended).
5. **Timestamps** — `created_at` / `updated_at` help debugging.

---

## Entity relationship overview

```text
users
  │
  │ 1
  │
  │ owns
  │
  ▼ *
projects
  │
  │ 1
  │
  │ contains
  │
  ▼ *
endpoints
  │
  ├──► check_results        (many)
  ├──► security_findings    (many)
  └──► recommendations      (many)
```

**In words:**

- A **user** owns many **projects**
- A **project** contains many **endpoints**
- An **endpoint** has many **check results**, **security findings**, and **recommendations**

---

## Proposed tables

> Auth libraries sometimes create their own tables (accounts, sessions, verification tokens).  
> Below focuses on **PulseGuard domain tables**. If we use a managed auth provider, `users` may be linked by an external `auth_user_id` instead of storing passwords ourselves.

### 1. `users`

Stores application users.

| Field | Type (conceptual) | Notes |
| ----- | ----------------- | ----- |
| `id` | UUID | Primary key |
| `email` | text | Unique |
| `name` | text | Display name (optional) |
| `auth_user_id` | text | Link to auth provider/library user id (if needed) |
| `created_at` | timestamp | |
| `updated_at` | timestamp | |

**Do not store plaintext passwords.** Prefer the auth library’s secure session/password handling.

---

### 2. `projects`

A workspace that groups endpoints.

| Field | Type (conceptual) | Notes |
| ----- | ----------------- | ----- |
| `id` | UUID | Primary key |
| `user_id` | UUID | Foreign key → `users.id` (owner) |
| `name` | text | Required |
| `description` | text | Optional |
| `created_at` | timestamp | |
| `updated_at` | timestamp | |

**Relationship:** many projects → one user.

---

### 3. `endpoints`

An API URL we monitor.

| Field | Type (conceptual) | Notes |
| ----- | ----------------- | ----- |
| `id` | UUID | Primary key |
| `project_id` | UUID | Foreign key → `projects.id` |
| `name` | text | Friendly label |
| `url` | text | Full URL to check |
| `method` | text | MVP default: `GET` |
| `is_active` | boolean | If false, skip scheduled checks |
| `check_interval_minutes` | integer | e.g. 5 or 15 |
| `last_checked_at` | timestamp | Nullable until first check |
| `last_status` | text | e.g. `up`, `down`, `unknown` |
| `created_at` | timestamp | |
| `updated_at` | timestamp | |

**MVP note:** Optional auth headers/tokens for private APIs are deferred. If added later, store encrypted secrets in a separate table — never in plain text.

**Relationship:** many endpoints → one project.

---

### 4. `check_results`

One row per health check attempt.

| Field | Type (conceptual) | Notes |
| ----- | ----------------- | ----- |
| `id` | UUID | Primary key |
| `endpoint_id` | UUID | Foreign key → `endpoints.id` |
| `checked_at` | timestamp | When the check ran |
| `success` | boolean | Did it meet healthy criteria? |
| `status_code` | integer | Nullable if network failed |
| `latency_ms` | integer | Response time |
| `error_message` | text | Timeout / DNS / connection errors |
| `triggered_by` | text | `manual`, `cron`, or `system` |

**Relationship:** many results → one endpoint.

**Indexes (important later):** `(endpoint_id, checked_at DESC)` for fast history queries.

---

### 5. `security_findings`

Results from basic security checks.

| Field | Type (conceptual) | Notes |
| ----- | ----------------- | ----- |
| `id` | UUID | Primary key |
| `endpoint_id` | UUID | Foreign key → `endpoints.id` |
| `check_name` | text | e.g. `https_required`, `missing_hsts` |
| `severity` | text | `info`, `low`, `medium`, `high` |
| `title` | text | Short label |
| `description` | text | What was found |
| `passed` | boolean | true = good / no issue |
| `scanned_at` | timestamp | |
| `raw_evidence` | text/json | Optional small evidence snippet (no secrets) |

**Relationship:** many findings → one endpoint.

---

### 6. `recommendations`

AI-generated advice stored for reuse.

| Field | Type (conceptual) | Notes |
| ----- | ----------------- | ----- |
| `id` | UUID | Primary key |
| `endpoint_id` | UUID | Foreign key → `endpoints.id` |
| `title` | text | Short headline |
| `summary` | text | Human-readable advice |
| `severity` | text | Suggested priority |
| `suggested_actions` | text/json | List of steps |
| `model` | text | Which AI model produced it |
| `source` | text | e.g. `checks+security` |
| `created_at` | timestamp | |

**Relationship:** many recommendations → one endpoint.

---

## Table relationships (summary)

| From | To | Type | Rule |
| ---- | -- | ---- | ---- |
| `users` | `projects` | one-to-many | Delete user → delete or cascade their projects (policy TBD) |
| `projects` | `endpoints` | one-to-many | Delete project → cascade delete endpoints |
| `endpoints` | `check_results` | one-to-many | Delete endpoint → cascade delete results |
| `endpoints` | `security_findings` | one-to-many | Cascade on endpoint delete |
| `endpoints` | `recommendations` | one-to-many | Cascade on endpoint delete |

**Authorization rule (application level):**  
When loading an endpoint, always join/check that `projects.user_id === current_user.id`. Never trust only `endpoint_id` from the URL.

---

## Example data walkthrough

1. Alice signs up → row in `users`
2. Alice creates “Payments API” → row in `projects`
3. Alice adds `https://api.example.com/health` → row in `endpoints`
4. Cron runs a check → row in `check_results`; `endpoints.last_status` updated
5. Security scan runs → rows in `security_findings`
6. Alice clicks “Generate recommendations” → row(s) in `recommendations`

---

## Fields we intentionally skip in MVP

| Idea | Why not now |
| ---- | ----------- |
| Organization / team tables | Single-owner projects first |
| Role / permission tables | No multi-member workspaces yet |
| Alert subscription tables | Alerts are post-MVP |
| Full raw response body storage | Large, sensitive, expensive — store only small evidence |
| Separate incident table | Derive “failing” from consecutive `check_results` first |

---

## Security considerations for data

| Topic | Guidance |
| ----- | -------- |
| PII | Emails are personal data — protect DB access |
| Secrets | Never store AI keys or DB credentials in tables |
| Evidence fields | Do not save Authorization headers or cookies from target APIs |
| Cascades | Deleting a project must not leave orphan endpoint data |
| Backups | Enable provider backups before real users |

---

## Implementation notes (for later coding phase)

- Prefer a typed ORM/query layer (for example Prisma or Drizzle) so schema matches TypeScript types
- Put schema migrations under version control
- Seed script optional for local demo data
- Keep SQL/ORM access inside `src/services` or `src/lib/db`, not inside React components

---

## Related documents

- [SYSTEM_ARCHITECTURE.md](./SYSTEM_ARCHITECTURE.md)
- [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md)
- [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md)
