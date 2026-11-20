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
| Security issues (missing HTTPS, weak headers, risky configs) stay hidden | Higher chance of attacks or data leaks |
| Logs and status codes are hard to interpret quickly | Junior developers waste time guessing |
| Advice is scattered across docs and chat tools | Fixes are inconsistent and slow |

PulseGuard AI aims to put **monitoring**, **basic security checks**, **failure history**, and **AI guidance** in one simple place.

---

## Target users

### Primary users (MVP)

- **Individual developers** building or maintaining APIs
- **Small product teams** (2–6 people) who need a shared view of API health

### Secondary users (later, not MVP)

- Security-focused engineers who want deeper scanning
- Larger companies that need advanced roles, SSO, and audit compliance

### User goals

| User wants to… | PulseGuard helps by… |
| -------------- | -------------------- |
| Know if an API is up | Running scheduled health checks |
| See how fast it responds | Recording latency and status codes |
| Catch obvious security gaps | Running basic security checks |
| Understand what to do next | Generating AI recommendations |
| Share status with teammates | Organizing APIs under projects |

---

## Main features (product vision)

These are the product pillars. The **first shippable version** is a smaller subset — see [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md).

1. **API monitoring** — register endpoints and check availability, status codes, and response time.
2. **Security checks** — detect common misconfigurations (for example missing HTTPS or important security headers).
3. **Failure tracking** — store check history and highlight when something starts failing.
4. **AI recommendations** — turn check/scan results into prioritized, readable advice.
5. **Projects & accounts** — sign in, create a project, and manage endpoints in one workspace.
6. **Simple alerts** (post-MVP stretch) — notify someone when an API fails repeatedly.

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
