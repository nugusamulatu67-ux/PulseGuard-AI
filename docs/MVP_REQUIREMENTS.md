# MVP requirements

**MVP = Minimum Viable Product** — the smallest useful version we can ship and learn from.

This document defines what **is** and **is not** in the first version. It must stay aligned with [PROJECT_OVERVIEW.md](./PROJECT_OVERVIEW.md), [SYSTEM_ARCHITECTURE.md](./SYSTEM_ARCHITECTURE.md), and [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md).

---

## MVP goals

Ship a working app where a signed-in user can:

- Create a **project**
- Add **API endpoints** to monitor
- Run **health checks** (manual + scheduled)
- See **results and history** on a simple dashboard
- Trigger **basic security checks**
- Receive **AI recommendations** based on those results

Keep the stack simple: one Next.js app, one PostgreSQL database, managed auth, and one AI provider path.

---

## Detailed MVP features

### 1. Authentication

| Feature | Description |
| ------- | ----------- |
| Sign up / sign in | Users can create an account and log in |
| Sign out | Users can end their session |
| Protected pages | Dashboard and project pages require login |

**Beginner note:** We will use a managed or well-documented auth library so we do not build password hashing and session security from scratch.

### 2. Projects

| Feature | Description |
| ------- | ----------- |
| Create project | Name + optional description |
| List projects | See all projects owned by the current user |
| Open project | View endpoints and recent check status |
| Delete project | Remove a project and its related data (or soft-delete) |

**MVP simplification:** One user owns their projects. Team invites and roles come later.

### 3. API endpoint management

| Feature | Description |
| ------- | ----------- |
| Add endpoint | URL, HTTP method (GET for MVP), optional display name |
| Edit endpoint | Update name/URL/settings |
| Delete endpoint | Remove from monitoring |
| List endpoints | Show status summary per endpoint |

**MVP simplification:** Focus on `GET` health checks first. Auth headers for private APIs can be a stretch goal if time allows (must be stored encrypted if implemented).

### 4. Health monitoring

| Feature | Description |
| ------- | ----------- |
| Manual check | “Check now” button |
| Scheduled check | Periodic checks (for example every 5–15 minutes via cron) |
| Record result | Status code, latency (ms), success/fail, timestamp, error message if any |
| History | Last N results per endpoint |

**Success rule (MVP):** HTTP status `2xx` or `3xx` = healthy; anything else or timeout = unhealthy.

### 5. Basic security checks

Lightweight checks only — not a full penetration testing tool.

| Check | What we look for |
| ----- | ---------------- |
| HTTPS | URL uses `https://` |
| Security headers | Presence of common headers (e.g. `Strict-Transport-Security`, `Content-Type-Options`, `X-Frame-Options` / `frame-ancestors`) |
| Redirect / TLS basics | Obvious misconfiguration signals from the response |

Results are stored as findings with severity (`info`, `low`, `medium`, `high`).

### 6. Failure visibility

| Feature | Description |
| ------- | ----------- |
| Latest status | Up / Down / Unknown on dashboard |
| Recent failures | List of failed checks |
| Simple incident flag | If consecutive checks fail, mark endpoint as “failing” |

### 7. AI recommendations

| Feature | Description |
| ------- | ----------- |
| Generate advice | Send recent check + security findings to an AI model |
| Store recommendation | Save title, summary, severity, and suggested actions |
| Show in UI | Display on endpoint or project detail page |

**MVP simplification:** One recommendation generation flow per endpoint (manual “Generate recommendations” button). No autonomous multi-agent system.

### 8. Dashboard UI

| Screen | Purpose |
| ------ | ------- |
| Landing / marketing home | Explain product (can stay simple) |
| Login / signup | Auth |
| Projects list | Entry point after login |
| Project detail | Endpoints + overall health |
| Endpoint detail | History, security findings, AI advice |

---

## Functional requirements

Functional requirements describe **what the system must do**.

| ID | Requirement |
| -- | ----------- |
| FR-01 | Users must authenticate before accessing projects and monitoring data |
| FR-02 | Users can create, view, update, and delete their own projects |
| FR-03 | Users can add, view, update, and delete endpoints inside a project |
| FR-04 | System can perform an HTTP health check against a registered endpoint URL |
| FR-05 | System stores each check result with timestamp, status, latency, and error text |
| FR-06 | System can run scheduled health checks for active endpoints |
| FR-07 | System can run basic security checks and store findings |
| FR-08 | System can call an AI provider with check/finding context and store recommendations |
| FR-09 | Users can view dashboards showing current status and recent history |
| FR-10 | Users can only access their own projects and related data |

---

## Non-functional requirements

Non-functional requirements describe **how well** the system should behave.

| ID | Category | Requirement |
| -- | -------- | ----------- |
| NFR-01 | Security | Secrets (DB URL, auth secret, AI keys) live in environment variables, never in git |
| NFR-02 | Security | All app pages that show private data require authentication |
| NFR-03 | Security | Server-side authorization: a user cannot read/write another user’s project by guessing IDs |
| NFR-04 | Reliability | Health checks handle timeouts and network errors without crashing the app |
| NFR-05 | Performance | Dashboard loads recent data quickly (target: under a few seconds for MVP data sizes) |
| NFR-06 | Usability | UI is understandable for junior developers; avoid jargon without explanation |
| NFR-07 | Maintainability | Code follows the folder structure in [FOLDER_STRUCTURE.md](./FOLDER_STRUCTURE.md) |
| NFR-08 | Observability | Important server errors are logged (full observability stack can come later) |
| NFR-09 | Portability | App runs locally with `.env.local` and can deploy to Vercel |

---

## Features excluded from the first version (out of scope)

Do **not** build these in MVP unless the whole team explicitly re-prioritizes:

| Excluded feature | Why wait |
| ---------------- | -------- |
| Team invites / roles / SSO | Adds auth complexity |
| Full OWASP / penetration testing | Too broad for beginners |
| Custom scripting / plugin system | Hard to secure and maintain |
| Mobile apps | Web-first is enough |
| Real-time WebSocket live graphs | Polling or page refresh is fine |
| Multi-region distributed probes | Overkill for MVP |
| Billing / subscriptions | Product first |
| Slack / PagerDuty integrations | Nice later; email optional stretch |
| Complex AI agents / autonomous remediations | Keep one recommendation call |
| Public API for third-party tools | Internal app first |
| GraphQL gateway | REST/route handlers are enough |

---

## Technical risks (MVP)

| Risk | Impact | Mitigation |
| ---- | ------ | ---------- |
| Checking untrusted URLs (SSRF) | Attacker could make our server call internal network addresses | Allowlist schemes (`https` only by default), block private IP ranges, set timeouts |
| Storing API tokens for private endpoints | Token theft if DB leaks | Defer tokens if possible; otherwise encrypt at rest and never show full token again |
| AI cost / rate limits | Unexpected bills or failures | Manual generate button; limit frequency; use one model via gateway |
| Cron reliability | Missed checks | Use platform cron (e.g. Vercel Cron); store last-run time; allow manual check |
| Auth mistakes | Account takeover / data leaks | Use a mature auth library; never roll crypto yourself |
| Scope creep | Team never ships | Follow this document; park ideas in “post-MVP” |

---

## Security considerations (MVP checklist)

- [ ] HTTPS only in production
- [ ] No secrets in the repository
- [ ] Auth on all private routes and API handlers
- [ ] Ownership checks on every project/endpoint query
- [ ] Input validation for URLs and form fields
- [ ] Timeouts on outbound HTTP checks
- [ ] SSRF protections for monitor targets
- [ ] AI prompts include only necessary data (avoid pasting secrets)

---

## Definition of done for MVP

- All FR-01 … FR-10 work for a happy path demo
- Core NFR security items above are addressed
- Docs still match the implemented behavior
- At least one teammate can run the app locally from README instructions

---

## Related documents

- [PROJECT_OVERVIEW.md](./PROJECT_OVERVIEW.md)
- [SYSTEM_ARCHITECTURE.md](./SYSTEM_ARCHITECTURE.md)
- [DATABASE_DESIGN.md](./DATABASE_DESIGN.md)
- [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md)
