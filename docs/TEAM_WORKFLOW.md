# Team workflow

How our small team collaborates on PulseGuard AI using **Git** and **GitHub**.

This expands [CONTRIBUTING.md](./CONTRIBUTING.md) with clearer beginner explanations. If the two ever disagree, **prefer this document** and then update CONTRIBUTING.

---

## Why this matters

Without a shared workflow, teammates overwrite each other, secrets leak into git, and nobody knows what is safe to deploy.

Our goals:

- Keep `main` stable
- Make changes in small, reviewable pieces
- Write code the whole team can understand

---

## Git and GitHub collaboration (basics)

| Term | Meaning |
| ---- | ------- |
| Repository (repo) | The project on GitHub |
| Commit | A saved snapshot of changes with a message |
| Branch | A separate line of work (like a notebook copy) |
| Pull Request (PR) | A request to merge your branch into `main` after review |
| `main` | The shared “good” branch |

### Daily habit

1. `git checkout main`
2. `git pull` (get latest)
3. Create a branch for your task
4. Commit often with clear messages
5. Push your branch
6. Open a PR
7. Wait for review / fix comments
8. Merge, then delete the branch

---

## Branching strategy

We use a **simple feature-branch** model (good for beginners).

| Branch type | Name pattern | Example | Purpose |
| ----------- | ------------ | ------- | ------- |
| Main | `main` | `main` | Stable code only |
| Feature | `feature/<short-name>` | `feature/add-project-form` | New functionality |
| Fix | `fix/<short-name>` | `fix/check-timeout` | Bug fixes |
| Docs | `docs/<short-name>` | `docs/mvp-requirements` | Documentation only |
| Chore | `chore/<short-name>` | `chore/update-env-example` | Tooling/cleanup |

### Rules

- Do **not** commit directly to `main` (use PRs)
- One branch ≈ one focused task
- Keep branches short-lived (days, not weeks)
- Rebase or merge `main` into your branch if it falls behind (ask a teammate if unsure)

---

## Pull request workflow

### Before opening a PR

- [ ] Code runs locally (`npm run dev`)
- [ ] `npm run lint` passes
- [ ] No secrets in the diff (`.env.local`, API keys, passwords)
- [ ] You tested the happy path of your change
- [ ] Docs updated if behavior/architecture changed

### PR title examples

- `Add project create form`
- `Fix health check timeout handling`
- `Document SSRF protections`

### PR description template

```markdown
## What
Briefly describe the change.

## Why
What problem does this solve?

## How to test
1. Steps a reviewer can follow
2. ...

## Notes
Anything deferred or risky?
```

### Review rules

- At least **one teammate review** when possible
- Reviewers check: correctness, clarity, security (authz, secrets), and MVP scope
- Author responds to comments or explains why not
- Merge only when checks/review are OK

### Merging

- Prefer **Squash and merge** for clean history (team can decide)
- Delete the branch after merge
- Pull `main` locally before starting the next task

---

## Coding conventions

### Language & style

- **TypeScript** with strict typing — avoid `any` unless you leave a short comment why
- Prefer clear names: `checkEndpointHealth` over `doStuff`
- Keep files focused; split when a file becomes hard to scroll/understand

### React / Next.js

- Default to **Server Components**
- Add `"use client"` only when needed (browser events, local state, etc.)
- Do not put secrets in client components
- Put business logic in `src/services`, not deep inside random UI files

### Folder placement (quick guide)

| Kind of code | Put it in |
| ------------ | --------- |
| Page / route | `src/app/...` |
| Reusable UI | `src/components/...` |
| Business logic | `src/services/...` |
| Helpers | `src/lib/...` |
| Shared types | `src/types/...` |
| Constants | `src/config/...` |

See [FOLDER_STRUCTURE.md](./FOLDER_STRUCTURE.md).

### Comments

- Comment **why**, not what is already obvious from code
- Explain security-sensitive decisions (e.g. why a URL is rejected)

### Commits

Use short imperative messages:

- `add endpoint health check service`
- `fix project ownership query`
- `update database design docs`

Avoid huge commits that mix unrelated features.

---

## Environment and secrets

| Do | Don’t |
| -- | ----- |
| Copy `.env.example` → `.env.local` | Commit `.env.local` |
| Share setup steps in docs | Share real API keys in Discord/chat screenshots |
| Use different keys for local/prod when possible | Hardcode secrets in source files |

If a secret is accidentally committed: **rotate/revoke it immediately**, then clean git history with a teammate’s help.

---

## Issue / task tracking (lightweight)

Even beginners benefit from a simple board (GitHub Issues/Projects):

| Label | Meaning |
| ----- | ------- |
| `phase-1` … `phase-7` | Matches [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md) |
| `bug` | Something broken |
| `docs` | Documentation |
| `blocked` | Waiting on another task/person |

Link PRs to issues when possible (`Closes #12`).

---

## Communication norms

- Ask early if a task seems bigger than expected
- If you change architecture assumptions, update the matching `docs/` file in the same PR
- Protect MVP scope: new ideas go to Phase 8 / backlog unless the team agrees

---

## Security habits for every PR

- [ ] Auth required on private routes/APIs?
- [ ] Ownership check for project/endpoint IDs?
- [ ] User input validated?
- [ ] Outbound URL checks still SSRF-safe?
- [ ] No new secret in the client bundle?

---

## Related documents

- [CONTRIBUTING.md](./CONTRIBUTING.md)
- [DEVELOPMENT_ROADMAP.md](./DEVELOPMENT_ROADMAP.md)
- [MVP_REQUIREMENTS.md](./MVP_REQUIREMENTS.md)
- [SYSTEM_ARCHITECTURE.md](./SYSTEM_ARCHITECTURE.md)
