# PulseGuard AI

AI-powered API security and reliability platform that helps developers monitor APIs, detect security vulnerabilities, identify failures, and receive AI-generated recommendations.

## Status

This repository currently contains the **project foundation only**. Application features have not been implemented yet.

## Tech stack

- [Next.js](https://nextjs.org/) (App Router)
- [TypeScript](https://www.typescriptlang.org/)
- [Tailwind CSS](https://tailwindcss.com/)
- [React](https://react.dev/)

## Getting started

### Prerequisites

- Node.js 20+ (Node.js 24 LTS recommended)
- npm 10+

### Setup

1. Clone the repository.
2. Install dependencies:

```bash
npm install
```

3. Copy the example environment file and fill in values as needed:

```bash
cp .env.example .env.local
```

4. Start the development server:

```bash
npm run dev
```

5. Open [http://localhost:3000](http://localhost:3000).

### Scripts

| Command         | Description                         |
| --------------- | ----------------------------------- |
| `npm run dev`   | Start the development server        |
| `npm run build` | Create a production build           |
| `npm run start` | Run the production server           |
| `npm run lint`  | Run ESLint                          |

## Project structure

```text
PulseGuard-AI/
├── docs/                 # Project documentation for the team
├── public/               # Static assets served as-is
├── src/
│   ├── app/              # Next.js App Router (pages, layouts, API routes)
│   ├── components/       # Reusable UI and layout components
│   ├── config/           # App configuration and constants
│   ├── hooks/            # Shared React hooks
│   ├── lib/              # Shared utilities and helpers
│   ├── services/         # External API / domain service clients
│   └── types/            # Shared TypeScript types and interfaces
├── .env.example          # Documented environment variable template
├── .gitignore
├── next.config.ts
├── package.json
└── tsconfig.json
```

See [docs/FOLDER_STRUCTURE.md](docs/FOLDER_STRUCTURE.md) for a detailed explanation of each directory.

## Documentation

Full index: [docs/README.md](docs/README.md)

| Document | Purpose |
| -------- | ------- |
| [docs/PROJECT_OVERVIEW.md](docs/PROJECT_OVERVIEW.md) | Purpose, problem, users, features |
| [docs/MVP_REQUIREMENTS.md](docs/MVP_REQUIREMENTS.md) | MVP scope and requirements |
| [docs/SYSTEM_ARCHITECTURE.md](docs/SYSTEM_ARCHITECTURE.md) | Detailed system architecture |
| [docs/DATABASE_DESIGN.md](docs/DATABASE_DESIGN.md) | Proposed database schema |
| [docs/DEVELOPMENT_ROADMAP.md](docs/DEVELOPMENT_ROADMAP.md) | Phased development plan |
| [docs/TEAM_WORKFLOW.md](docs/TEAM_WORKFLOW.md) | Git/GitHub and coding workflow |
| [docs/FOLDER_STRUCTURE.md](docs/FOLDER_STRUCTURE.md) | Folder and file conventions |

## Environment variables

Copy `.env.example` to `.env.local` for local development. Never commit real secrets.

## License

Proprietary — all rights reserved unless otherwise stated.
