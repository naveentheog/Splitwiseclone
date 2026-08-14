# Splitwise Clone

A self-hosted expense-sharing app. Create groups, log shared expenses, split them
between people, and track who owes whom — then settle up.

## Features

- **Groups** — create groups and add members
- **Expenses** — log an expense, choose who paid and who shares it
- **Split types** — equal, exact amounts, percentage, shares, and adjustments
- **Balances** — running totals of what each person owes or is owed
- **Debt simplification** — reduces the number of transfers needed to settle a
  group (optional, per group)
- **Settlements** — record payments between members
- **Non-group expenses** — split with a single person without creating a group
- **Multi-currency** — expenses in different currencies
- **Activity feed** — history of edits and deletions
- **PWA** — installable, with push notifications

## Tech stack

| Layer     | Technology                          |
| --------- | ----------------------------------- |
| Framework | Next.js (Pages Router) + TypeScript |
| API       | tRPC                                |
| Database  | PostgreSQL                          |
| ORM       | Prisma                              |
| Auth      | NextAuth (email, Google, OIDC)      |
| Styling   | Tailwind CSS                        |
| Testing   | Jest                                |
| Runtime   | Node.js, pnpm                       |
| Deploy    | Docker / Docker Compose             |

## How the data model works

Expenses are the source of truth. Balances are **derived** from expenses and
settlements rather than stored, which keeps them consistent.

- `Expense` — description, total amount, who paid, split type
- `ExpenseParticipant` — one row per person, with the amount they owe
- `Settlement` — a payment recorded from one person to another
- `BalanceView` — a database view that computes balances from the above

Money is stored as `BigInt` in minor units (paise/cents), never as a float.
This is deliberate — floating-point arithmetic loses precision and produces
balances that slowly stop adding up.

## Getting started

**Requirements:** Node.js, pnpm, Docker.

```bash
# 1. Install dependencies
pnpm install

# 2. Configure environment
cp .env.example .env
# Set POSTGRES_PASSWORD, DATABASE_URL and NEXTAUTH_SECRET at minimum

# 3. Start Postgres and run migrations
pnpm dx:up
pnpm db:dev

# 4. Start the dev server
pnpm dev
```

The app runs at `http://localhost:3000`.

Shortcut: `pnpm d` runs install, database, migrations and dev server in one go.

### Useful commands

| Command          | Description                       |
| ---------------- | --------------------------------- |
| `pnpm dev`       | Start the dev server              |
| `pnpm build`     | Production build                  |
| `pnpm test`      | Run tests                         |
| `pnpm db:studio` | Open Prisma Studio to browse data |
| `pnpm db:seed`   | Seed sample data                  |
| `pnpm dx:down`   | Stop the database container       |

## Project structure

```
prisma/          Database schema, migrations and views
src/pages/       Routes and page components
src/server/      tRPC routers and server-side logic
src/components/  UI components
src/lib/         Shared utilities
docker/          Compose files for dev and production
docs/            Configuration and feature documentation
```

## Configuration

Further setup notes live in `docs/`:

- `CONFIGURATION.md` — environment variables
- `AUTHENTICATION.md` — auth providers
- `CURRENCY_CONVERSIONS.md` — multi-currency setup
- `RECURRING_TRANSACTIONS.md` — recurring expenses

## Licence and attribution

This project is based on [SplitPro](https://github.com/oss-apps/split-pro), an
open source Splitwise alternative, used under the MIT Licence
(Copyright &copy; 2024 OSS Apps).

The original licence is retained in full in [`LICENSE`](LICENSE). See
[`ATTRIBUTION.md`](ATTRIBUTION.md) for details.
