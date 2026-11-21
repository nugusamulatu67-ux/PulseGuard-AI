# Roadmap

High-level milestones for PulseGuard AI. Dates and ownership can be filled in by the team.

For task-level phases and dependencies, use [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md). First-release scope is defined there and in [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md). This checklist does not add features those documents place after the MVP.

## Milestone 0 — Foundation (current)

- [x] Next.js + TypeScript + App Router
- [x] Tailwind CSS
- [x] Scalable folder structure
- [x] README, `.gitignore`, `.env.example`, and `docs/`

## Milestone 1 — Core shell

- [ ] App shell / navigation
- [ ] Authentication and protected routes
- [ ] Individual developer projects (one owner per project)
- [ ] Base design system components

## Milestone 2 — API monitoring

- [ ] Register and manage `http://` and `https://` endpoints
- [ ] Health / latency checks
- [ ] Status history and basic dashboards

## Milestone 3 — Security & failures

- [ ] Vulnerability and misconfiguration checks, including insecure HTTP (HTTP registration stays allowed)
- [ ] Repeated-failure highlight derived from recent check results (not a stored status)

## Milestone 4 — AI recommendations

- [ ] AI analysis pipeline
- [ ] Prioritized remediation suggestions
- [ ] Developer-facing recommendation UI

## Milestone 5 — Polish and release

Matches Phase 7 in [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md).

- [ ] Empty, error, and loading states
- [ ] Review of ownership checks, SSRF protections, and timeouts
- [ ] Production deployment checklist

## After the first release

Not part of the current architecture. See Phase 8 in [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md).

- [ ] Team workspaces, invitations, and role management
- [ ] Email and Slack notifications
- [ ] Stored incident records and root-cause analysis
- [ ] Full observability stack and audit logs
