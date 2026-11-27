# Agents (Cursor & automation)

This file tells **AI assistants and automated tools** how to work effectively in this repository. Keep it accurate; update it when workflows or constraints change.

## What this project is

**Sanctum** is a local-first D&D 5e campaign manager (Next.js 16 + React 19 + SQLite/Drizzle, optional Electron desktop). No accounts, no cloud, no AI. Optional LAN **player view** only. Product overview: [`README.md`](./README.md).

## Read first

1. [`README.md`](./README.md) — purpose, features, scripts, data paths, LAN sharing.
2. [`agent_docs/ARCHITECTURE.md`](./agent_docs/ARCHITECTURE.md) — components, runtime modes, code map.
3. [`agent_docs/SECURITY.md`](./agent_docs/SECURITY.md) — LAN guard, packaging, non-negotiables.
4. [`agent_docs/product-specs/`](./agent_docs/product-specs/) — requirements and onboarding.

Also consult as relevant under `agent_docs/`: `DESIGN.md`, `FRONTEND.md`, `RELIABILITY.md`, `TESTING.md`, `OPERATIONS.md`, `DATA.md`, `PLANS.md`.

Harness map: [`agent_docs/README.md`](./agent_docs/README.md).

## Repo layout (quick)

| Path | Role |
|------|------|
| `src/app/` | App Router pages + `api/` route handlers |
| `src/components/` | Feature UI + `ui/` (shadcn) |
| `src/lib/` | Domain logic (combat, dice, lan, validators, i18n, …) |
| `src/db/` | Drizzle schema + DB bootstrap |
| `drizzle/` | SQL migrations (auto-applied on open) |
| `electron/` | Desktop shell |
| `scripts/` | LAN guard, desktop build, asset copy |
| `agent_docs/` | Agent harness docs and plans |
| `docs/` | Human release/dev notes (not the harness) |

## Defaults for code changes

- Prefer **small, focused changes** that match existing style and patterns (`apiRoute`, Zod in `validators.ts`, `collectionHandlers` / `itemHandlers`).
- Do not expand scope beyond the task unless the user explicitly asks.
- After substantive edits, run `npm run lint`; there is no automated unit suite yet — manually smoke critical paths you touch (see `agent_docs/TESTING.md`).
- Do not commit secrets, API keys, `data/`, `backups/`, `uploads/`, or `lan.json`. See `agent_docs/SECURITY.md`.
- **JSON columns:** never `.default("[]")` / stringified JSON on `mode: "json"` fields — use real values (`.default([])`). See `docs/JSON_COLUMN_DEFAULTS_FIX.md`.
- **Player view:** filter on the server; do not rely on client hiding for fog, hidden tokens, or exact monster HP.
- **i18n:** add UI strings to all locale files under `src/lib/i18n/`.
- **Desktop bundle:** do not weaken `outputFileTracingExcludes` or the desktop allowlist in ways that could ship `src/`, `uploads/`, or docs into the package.

## Where to put work

| Kind of work | Location |
|--------------|----------|
| Execution plans (step-by-step implementation) | `agent_docs/exec-plans/active/` → move to `completed/` when done |
| Detailed design (beyond `ARCHITECTURE.md`) | `agent_docs/design-docs/` |
| User-facing requirements | `agent_docs/product-specs/` |
| Generated or synced artifacts (schemas, API dumps) | `agent_docs/generated/` |
| External specs, PDFs, diagrams | `agent_docs/references/` |
| Release / itch / one-off engineering notes | `docs/` (repo root) |

## Planning workflow

- **`agent_docs/PLANS.md`** is the high-level index; detailed plans belong under `agent_docs/exec-plans/`.
- When starting a multi-step feature, add or update a plan in `active/`, implement, then archive to `completed/`.
- Track deferred fixes and cleanup in `agent_docs/exec-plans/tech-debt-tracker.md`.

## Questions

If requirements conflict with the codebase or with each other, **ask for clarification** before guessing, especially for security (LAN guard), data integrity (migrations/JSON defaults), and player-visible behavior.
