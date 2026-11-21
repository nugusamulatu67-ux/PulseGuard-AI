# Architecture overview

PulseGuard AI is planned as an AI-powered API security and reliability platform.

This document describes the **intended** high-level architecture for the first release. Implementation work has not started beyond the Next.js foundation.

[MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md) and [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md) decide what is in that release. This summary should not add features those documents schedule for later.

## Product goals

- Monitor API health, latency, and availability
- Detect security vulnerabilities and risky configurations
- Identify failures and regressions
- Generate actionable AI recommendations for developers

## Planned layers

```text
┌─────────────────────────────────────────────┐
│  Presentation (Next.js App Router / UI)     │
├─────────────────────────────────────────────┤
│  API routes / server actions                │
├─────────────────────────────────────────────┤
│  Domain services (monitor, scan, advise)    │
├─────────────────────────────────────────────┤
│  Integrations (AI, storage, auth)           │
└─────────────────────────────────────────────┘
```

| Layer | Responsibility |
| ----- | -------------- |
| Presentation | Dashboards, settings, and developer-facing views |
| API / server | Request handling, validation, and orchestration |
| Domain services | Business logic for monitoring, scanning, and recommendations |
| Integrations | External providers for the first release (AI models, database, auth) |

## Planned modules (not yet implemented)

- **API monitoring** — endpoint checks, uptime, latency, and status tracking for one developer’s endpoints
- **Security scanning** — misconfiguration detection, including insecure HTTP. Both `http://` and `https://` can be registered. HTTPS remains supported
- **Failure visibility** — the dashboard derives a “failing” highlight from recent check results. `last_status` stores only `up`, `down`, or `unknown`
- **AI recommendations** — prioritized remediation guidance
- **Auth and projects** — individual accounts and per-user project ownership

### Later, not part of this architecture

Scheduled in [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md) Phase 8, after the first release:

- Team workspaces, invitations, and role management
- Email and Slack notifications
- Stored incident records and root-cause analysis

## Runtime notes

- Prefer the default Node.js runtime on Vercel Fluid Compute for server routes.
- Keep secrets in environment variables (see `.env.example`).
- Shared types belong in `src/types`; shared helpers belong in `src/lib`.

## Detailed planning docs

This file is a short summary. Prefer these for implementation planning:

- [SYSTEM_ARCHITECTURE.md](./SYSTEM_ARCHITECTURE.md) — full architecture
- [DATABASE_DESIGN.md](./DATABASE_DESIGN.md) — schema proposal
- [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md) — MVP scope
- [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md) — build phases

## Next steps

See [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md) (detailed) or [ROADMAP.md](./ROADMAP.md) (short checklist).
