# Development roadmap

This is the **build order** for PulseGuard AI.  
It expands [ROADMAP.md](./ROADMAP.md) with tasks and dependencies suitable for a small beginner team.

**Rule:** Finish (or stabilize) a phase before starting the next one. Parallel work is OK only when dependencies allow it.

This file and [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md) are the authoritative scope for the first release. Shorter docs such as [ARCHITECTURE.md](./ARCHITECTURE.md) and [ROADMAP.md](./ROADMAP.md) must follow them.

---

## Phase map

```text
Phase 0 Foundation          ✅ done
        │
        ▼
Phase 1 Auth + app shell
        │
        ▼
Phase 2 Database + projects/endpoints CRUD
        │
        ▼
Phase 3 Health monitoring (manual + history)
        │
        ▼
Phase 4 Scheduled checks (cron)
        │
        ▼
Phase 5 Basic security checks
        │
        ▼
Phase 6 AI recommendations
        │
        ▼
Phase 7 Polish, hardening, MVP demo
        │
        ▼
Phase 8 Post-MVP (only after MVP ships)
```

---

## Phase 0 — Foundation (complete)

**Goal:** Empty but runnable Next.js app with docs and structure.

| Task | Status | Depends on |
| ---- | ------ | ---------- |
| Next.js + TypeScript + App Router | Done | — |
| Tailwind CSS | Done | — |
| Folder structure | Done | — |
| README, `.gitignore`, `.env.example` | Done | — |
| Planning docs in `docs/` | In progress / done with this phase of writing | Phase 0 code |

---

## Phase 1 — Auth + app shell

**Goal:** Users can sign in and see a protected empty dashboard layout.

| Task ID | Task | Depends on |
| ------- | ---- | ---------- |
| P1-1 | Choose auth approach (library/provider) and document env vars | Phase 0 |
| P1-2 | Implement sign up / sign in / sign out | P1-1 |
| P1-3 | Protect dashboard routes | P1-2 |
| P1-4 | Build simple app shell (nav + layout) | P1-2 |
| P1-5 | Landing page CTA → auth | P1-2 |

**Exit criteria:** Logged-out users cannot open `/dashboard` (or equivalent). Logged-in users see a shell.

---

## Phase 2 — Database + projects/endpoints CRUD

**Goal:** Persist projects and endpoints for the signed-in user.

| Task ID | Task | Depends on |
| ------- | ---- | ---------- |
| P2-1 | Provision PostgreSQL + set `DATABASE_URL` | Phase 0 |
| P2-2 | Add DB client/ORM + migrations for MVP tables | P2-1, [DATABASE_DESIGN.md](./DATABASE_DESIGN.md) |
| P2-3 | Link auth user ↔ `users` table | P1-2, P2-2 |
| P2-4 | Projects CRUD (create/list/view/delete) with ownership checks | P2-3, P1-3 |
| P2-5 | Endpoints CRUD inside a project | P2-4 |
| P2-6 | URL validation helpers (`http` or `https`, basic format, block private addresses). Allow HTTP registrations; do not require HTTPS | P2-5 |

**Exit criteria:** User can create a project, add an endpoint URL, and see it listed after refresh.

---

## Phase 3 — Health monitoring (manual + history)

**Goal:** “Check now” works and results appear in history.

| Task ID | Task | Depends on |
| ------- | ---- | ---------- |
| P3-1 | Outbound HTTP check service (timeouts, status, latency) | P2-5 |
| P3-2 | SSRF protections (block private IPs, etc.) | P3-1 |
| P3-3 | Save `check_results` and, in the same operation, update cached `last_status` (`up`, `down`, or `unknown`) and `last_checked_at`. Do not store `failing` | P3-1, P2-2 |
| P3-4 | “Check now” button + API route/server action | P3-3, P1-3 |
| P3-5 | Endpoint detail page: recent history table | P3-3 |
| P3-6 | Project dashboard status summary from cached `last_status`, plus a “failing” highlight derived from consecutive recent `check_results` | P3-3 |

**Exit criteria:** Manual check against a public URL shows success/failure and latency.

---

## Phase 4 — Scheduled checks

**Goal:** Active endpoints are checked automatically.

| Task ID | Task | Depends on |
| ------- | ---- | ---------- |
| P4-1 | Cron route `/api/cron/run-checks` | P3-3 |
| P4-2 | Secure cron with secret token | P4-1 |
| P4-3 | Select due endpoints using `check_interval_minutes` / `last_checked_at` | P4-1 |
| P4-4 | Configure platform cron schedule | P4-2 |

**Exit criteria:** Without clicking the UI, a due endpoint gets a new `check_results` row.

---

## Phase 5 — Basic security checks

**Goal:** Run lightweight security checks and show findings.

| Task ID | Task | Depends on |
| ------- | ---- | ---------- |
| P5-1 | Security check service: report insecure HTTP, check security headers, and treat HTTPS as the passing transport | P3-1 |
| P5-2 | Store `security_findings` | P5-1, P2-2 |
| P5-3 | “Scan security” action in UI | P5-2 |
| P5-4 | Findings list on endpoint page | P5-2 |

**Exit criteria:** A registered `http://` endpoint or a header-weak endpoint produces visible findings. HTTPS endpoints pass the transport check. HTTP registration is allowed.

---

## Phase 6 — AI recommendations

**Goal:** Generate and display stored AI advice from real data.

| Task ID | Task | Depends on |
| ------- | ---- | ---------- |
| P6-1 | Configure AI Gateway env (`AI_GATEWAY_API_KEY`, `AI_MODEL`) | Phase 0 |
| P6-2 | Recommendation service: build prompt from checks + findings | P3-3, P5-2, P6-1 |
| P6-3 | Save `recommendations` rows | P6-2, P2-2 |
| P6-4 | “Generate recommendations” button | P6-3 |
| P6-5 | Display recommendations in UI | P6-3 |
| P6-6 | Basic rate limiting / cooldown per endpoint | P6-4 |

**Exit criteria:** After checks/findings exist, user can generate readable advice without exposing secrets to the client.

---

## Phase 7 — Polish, hardening, MVP demo

**Goal:** Make the MVP safe and demoable.

| Task ID | Task | Depends on |
| ------- | ---- | ---------- |
| P7-1 | Empty/error/loading states on main screens | Phases 2–6 |
| P7-2 | Review ownership checks on all mutations | Phases 2–6 |
| P7-3 | Review SSRF + timeout settings | Phase 3–4 |
| P7-4 | README update for real setup steps | P7-1 |
| P7-5 | End-to-end team demo script | P7-1 |
| P7-6 | Deploy preview/production environment | P2-1, P1-2 |

**Exit criteria:** Team can demo FR-01…FR-10 from [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md).

---

## Phase 8 — Post-MVP (backlog)

Only after MVP ships. These items are not part of the current architecture or the first release:

- Team workspaces, invitations, and role management
- Email and Slack notifications (PagerDuty as well, if added)
- Encrypted credentials for private endpoints
- Richer security checks
- Charts / longer retention analytics
- Billing
- Stored incident records and root-cause analysis
- Full observability stack and audit logs

---

## Dependency highlights (read this)

| If you are building… | You must already have… |
| -------------------- | ---------------------- |
| Endpoint forms | Auth + projects + database |
| Manual checks | Endpoints saved in DB |
| Cron checks | Manual check service working |
| Security findings | Outbound HTTP helper |
| AI recommendations | Some checks and/or findings to analyze |
| Production deploy | Env vars + DB + auth configured |

```text
Auth ──► Projects/Endpoints ──► Manual checks ──► Cron
                                 │
                                 ├──► Security checks ──► AI recommendations
                                 └─────────────────────► AI recommendations
```

---

## Team capacity tip

For 2–4 beginners:

- Assign **one phase owner**, others help with UI or docs
- Prefer vertical slices (one feature working end-to-end) over many half-finished pieces
- Do not start Phase 6 until Phase 3 data exists — AI without real inputs is useless for demos

---

## Related documents

- [ROADMAP.md](./ROADMAP.md) — shorter milestone checklist
- [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md)
- [TEAM_WORKFLOW.md](./TEAM_WORKFLOW.md)
