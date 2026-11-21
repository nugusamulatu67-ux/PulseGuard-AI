# Project overview

**Product name:** PulseGuard AI  
**Current stage:** Planning and architecture (foundation code exists; features not built yet)  
**Audience:** Beginner-friendly team documentation

---

## Project purpose

PulseGuard AI helps developers **watch their APIs**, **spot security and reliability problems early**, and get **plain-language AI recommendations** on what to fix next.

In short: it is a dashboard + checker + advisor for APIs.

---

## Problem statement

Modern apps depend on many APIs. When something breaks or is insecure, teams often find out late — through angry users, production outages, or security reviews.

Common pain points:

| Problem | Why it hurts |
| ------- | ------------ |
| APIs go down or slow down without warning | Users see errors; developers scramble |
| Security issues (plain HTTP, weak headers, risky configs) stay hidden | Higher chance of attacks or data leaks |
| Logs and status codes are hard to interpret quickly | Junior developers waste time guessing |
| Advice is scattered across docs and chat tools | Fixes are inconsistent and slow |

PulseGuard AI aims to put **monitoring**, **basic security checks**, **failure history**, and **AI guidance** in one simple place.

---

## Target users

### Primary users (MVP)

- **Individual developers** building or maintaining their own APIs

Each account owns only its own projects. The first release has no shared team login.

### Later users (post-MVP)

- **Small product teams** (2–6 people) who need a shared view of API health
- Security-focused engineers who want deeper scanning
- Larger companies that need team workspaces, invitations, roles, SSO, and audit compliance

### User goals

| User wants to… | PulseGuard helps by… |
| -------------- | -------------------- |
| Know if an API is up | Running scheduled health checks |
| See how fast it responds | Recording latency and status codes |
| Catch obvious security gaps, including plain HTTP | Running basic security checks and reporting insecure HTTP |
| Understand what to do next | Generating AI recommendations |
| Keep related endpoints together | Organizing APIs under a personal project |

---

## Main features (product vision)

These are the first-release pillars. Scope details live in [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md).

1. **API monitoring** — register endpoints and check availability, status codes, and response time.
2. **Security checks** — accept both HTTP and HTTPS endpoints. Report plain HTTP as an insecure connection, and detect other common misconfigurations (for example missing security headers). HTTPS remains fully supported.
3. **Failure tracking** — store every check result. The dashboard highlights an endpoint as failing when recent results fail repeatedly. That label is calculated from history. It is not saved on the endpoint.
4. **AI recommendations** — turn check/scan results into prioritized, readable advice.
5. **Projects & accounts** — individual developer accounts. Sign in, create a project, and manage endpoints you own.

**Not in the first release:** team workspaces, invitations, role management, and email or Slack notifications. Those are post-MVP. The first release shows status, failures, findings, and recommendations on the dashboard only.

---

## Success for the MVP

The MVP is successful if a developer can:

1. Sign up / sign in  
2. Create a project  
3. Add an API endpoint  
4. Run or schedule checks  
5. See results on a dashboard  
6. Read at least one AI recommendation based on real check data  

---

## Related documents

| Document | What it covers |
| -------- | -------------- |
| [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md) | Exact MVP scope |
| [SYSTEM_ARCHITECTURE.md](./SYSTEM_ARCHITECTURE.md) | How the system is built |
| [DATABASE_DESIGN.md](./DATABASE_DESIGN.md) | Tables and relationships |
| [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md) | Build order |
| [TEAM_WORKFLOW.md](./TEAM_WORKFLOW.md) | How we collaborate |
