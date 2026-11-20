# Contributing

Guidelines for developers working on PulseGuard AI.

## Branching

- `main` — stable foundation and released work
- Feature branches — `feature/<short-name>`
- Fixes — `fix/<short-name>`
- Docs — `docs/<short-name>`

Open a pull request into `main` for review before merging.

## Local workflow

1. Pull the latest `main`.
2. Create a branch for your change.
3. Install dependencies with `npm install`.
4. Copy `.env.example` to `.env.local` if you need env vars.
5. Run `npm run dev` while developing.
6. Run `npm run lint` before opening a PR.
7. Keep PRs focused and reasonably small.

## Coding guidelines

- TypeScript strict mode is enabled — avoid `any` unless justified.
- Prefer clear names over abbreviations.
- Keep components small and colocated with related UI when possible.
- Put shared types in `src/types` and shared helpers in `src/lib`.
- Do not commit secrets, API keys, or `.env.local`.
- Do not implement speculative features outside the agreed roadmap without discussion.

## Commit messages

Use short, imperative summaries:

- `add monitoring service scaffold`
- `fix env example for AI gateway key`
- `update folder structure docs`

## Reviews

- Describe the problem and approach in the PR body.
- Call out any follow-up work that is intentionally deferred.
- Request review from at least one teammate before merge when possible.
