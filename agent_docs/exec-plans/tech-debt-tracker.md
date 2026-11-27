# Technical debt tracker

Deferred work, shortcuts, and known issues. Review when planning milestones.

## How to use

- Add rows with impact and a proposed fix.
- Link execution plans or issues when they exist.
- Move resolved items to the archive section.

## Active items

| ID | Area | Description | Impact | Proposed fix | Added |
|----|------|-------------|--------|--------------|-------|
| TD-001 | testing | No unit/integration/e2e suite; `lan-guard` `decide` is exportable but unused | High for regressions (LAN, schema defaults) | Add Vitest (or similar); matrix-test `decide`; smoke player payload filtering | 2026-11-26 |
| TD-002 | ci | No PR lint/test workflow; release workflow is tag-only | Medium | Add CI job: `npm run lint` (+ tests when TD-001 lands) | 2026-11-26 |
| TD-003 | release | Windows desktop builds are local-only | Medium | Add `windows-latest` job to `release-builds.yml` | 2026-11-26 |
| TD-004 | data | Polymorphic spell/inventory ownership has no SQL FK; relies on `cascadeOwned` | Medium | Audit all delete paths; consider tighter constraints or tests | 2026-11-26 |
| TD-005 | security/ops | LAN bind changes need desktop restart; `next dev`/`start` guard load timing leaves a brief uncovered window | Low–medium | Document for operators; optionally delay listen until guard installed in all entrypoints | 2026-11-26 |
| TD-006 | packaging | macOS/Windows builds unsigned (Gatekeeper/SmartScreen friction) | Low (UX) | Notarize / Authenticode when budget allows | 2026-11-26 |
| TD-007 | data | Historical JSON default double-encoding fixed; risk of reintroduction on new columns | Medium if repeated | Code review checklist + unit test on schema defaults; keep `docs/JSON_COLUMN_DEFAULTS_FIX.md` linked | 2026-11-26 |

## Resolved (optional archive)

| ID | Resolved | Notes |
|----|----------|-------|
| — | — | JSON defaults for `rulebooks` / `currency` repaired in-schema + `drizzle/0005_json_default_repair.sql` (see `docs/JSON_COLUMN_DEFAULTS_FIX.md`) |
