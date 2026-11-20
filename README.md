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

| Document | Purpose |
| -------- | ------- |
| [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) | High-level system design and planned modules |
| [docs/FOLDER_STRUCTURE.md](docs/FOLDER_STRUCTURE.md) | Folder and file conventions |
| [docs/CONTRIBUTING.md](docs/CONTRIBUTING.md) | Team workflow and coding guidelines |
| [docs/ROADMAP.md](docs/ROADMAP.md) | Planned feature milestones |

## Environment variables

Copy `.env.example` to `.env.local` for local development. Never commit real secrets.

## License

Proprietary — all rights reserved unless otherwise stated.
