# Architecture overview

PulseGuard AI is planned as an AI-powered API security and reliability platform.

This document describes the **intended** high-level architecture. Implementation work has not started beyond the Next.js foundation.

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
│  Integrations (AI, storage, auth, alerts)   │
└─────────────────────────────────────────────┘
```

| Layer | Responsibility |
| ----- | -------------- |
| Presentation | Dashboards, settings, and developer-facing views |
| API / server | Request handling, validation, and orchestration |
| Domain services | Business logic for monitoring, scanning, and recommendations |
| Integrations | External providers (AI models, databases, auth, notifications) |

## Planned modules (not yet implemented)

- **API monitoring** — endpoint checks, uptime, latency, and status tracking
- **Security scanning** — vulnerability and misconfiguration detection
- **Failure analysis** — incident detection and root-cause signals
- **AI recommendations** — prioritized remediation guidance
- **Auth & workspaces** — multi-user access and project isolation
- **Alerting** — notifications when issues are detected

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
