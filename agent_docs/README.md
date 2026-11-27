# Agent documentation (Sanctum)

Harness docs for **humans and AI agents** working in this repository. Start with root [`AGENTS.md`](../AGENTS.md), then the files below as needed.

Product-facing overview for end users and contributors: [`../README.md`](../README.md).

## Root documents (quick reference)

| File | Purpose |
|------|---------|
| [`../AGENTS.md`](../AGENTS.md) | Instructions for AI assistants and automation |
| [`ARCHITECTURE.md`](./ARCHITECTURE.md) | System structure, boundaries, and major decisions |
| [`DESIGN.md`](./DESIGN.md) | Product and technical design principles |
| [`FRONTEND.md`](./FRONTEND.md) | UI/UX conventions and frontend stack |
| [`PLANS.md`](./PLANS.md) | Planning index; detail lives in `exec-plans/` |
| [`RELIABILITY.md`](./RELIABILITY.md) | Availability, resilience, and operational quality |
| [`SECURITY.md`](./SECURITY.md) | Threat model, LAN sharing, secure development |
| [`TESTING.md`](./TESTING.md) | Test strategy and quality gates |
| [`OPERATIONS.md`](./OPERATIONS.md) | Dev, desktop builds, backups, release |
| [`DATA.md`](./DATA.md) | SQLite/Drizzle models, migrations, paths |

## Directory map

| Path | Contents |
|------|----------|
| `product-specs/` | User-facing requirements and onboarding |
| `exec-plans/active/` | In-progress multi-step implementation plans |
| `exec-plans/completed/` | Finished plans (archive) |
| `exec-plans/tech-debt-tracker.md` | Deferred fixes and known debt |
| `design-docs/` | Detailed designs beyond `ARCHITECTURE.md` |
| `generated/` | Synced artifacts (e.g. schema snapshot) |
| `references/` | External specs, PDFs, diagrams |

## Sibling `docs/` at repo root

Release notes, itch packaging, and one-off engineering notes live under [`../docs/`](../docs/) (for example `docs/ITCH_RELEASE.md`, `docs/JSON_COLUMN_DEFAULTS_FIX.md`). Do not confuse that folder with this harness.
