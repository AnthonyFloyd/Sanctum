# Data

Data modeling, storage, and migration practices for Sanctum. Human-readable table list: `generated/db-schema.md`. Source of truth: `src/db/schema.ts` + `drizzle/*.sql`.

## Stores

| Store | Purpose | Access pattern |
|-------|---------|----------------|
| SQLite `sanctum.db` | All campaign and app state | OLTP via Drizzle + `better-sqlite3` (WAL, `foreign_keys=ON`) |
| `uploads/` | Images, audio, PDFs | Filesystem; served by `/uploads/[filename]` |
| `backups/` | Daily DB snapshots | Write on open; listed via backups API |
| `lan.json` | LAN sharing flag + share token | Read by guard and `src/lib/lan.ts` |
| `app_settings` table | KV (welcome flag, last seen version, etc.) | `src/lib/app-settings.ts` |

Data root: `SANCTUM_DATA_ROOT` or `process.cwd()` — see `src/lib/paths.ts`.

## Conventions

- **Naming:** `snake_case` SQL tables/columns; Drizzle exports in camelCase (`combatParticipants` → `combat_participants`).
- **Timestamps:** Shared `createdAt` / `updatedAt` helpers (`timestamp_ms`) on most entities.
- **Campaign scoping:** Most domain tables carry `campaignId`; list APIs take `?campaignId=`.
- **JSON columns:** `text(..., { mode: "json" }).$type<T>()`. Pass **real** defaults (`.default([])` or `.$defaultFn(() => [])`), never `.default("[]")` — that double-encodes. See `docs/JSON_COLUMN_DEFAULTS_FIX.md` and repair migration `drizzle/0005_json_default_repair.sql`.
- **Polymorphic ownership:** `spells`, `spell_slots`, `inventory_items` use `ownerId` + `ownerType` (`character` | `npc`). Deletes cascade in `src/lib/crud.ts` (`cascadeOwned`), not via SQL FK.
- **Soft delete:** Not used generally; deletes are hard deletes (bulk delete + undo where the UI implements it).
- **Source field:** Empty = shipped/hand entry; `"homebrew"` for DM-authored content in homebrew tools.

## Migrations

1. Edit `src/db/schema.ts`.
2. `npm run db:generate` → new SQL under `drizzle/`.
3. App applies migrations automatically on DB open (`src/db/index.ts`); or `npm run db:migrate`.
4. Update `agent_docs/generated/db-schema.md` when the table set changes meaningfully.
5. Keep generated SQL and schema in sync; avoid hand-editing applied migrations unless adding a data repair (like `0005`).

## Import / export

- **Campaign JSON:** Full campaign portability including maps, fog, soundscapes, mind maps, homebrew (`src/lib/campaign-data.ts`).
- **Markdown / Obsidian:** One-way import; vault never written (`src/lib/markdown-import.ts`, `/api/import`).
- **Character PDF:** Fillable form fields in; Sanctum-drawn fillable PDF out (`src/lib/pdf-character-sheet*.ts`).
- **Sample campaign:** Seeded English demo (*The Lost Souls*) via `src/lib/sample-campaign.ts`.

## Privacy & retention

- Everything is local to the DM's machine. Retention is the DM's filesystem policy.
- Sharing a share link exposes filtered player state on the LAN until regenerated or sharing is disabled.
- Align any new telemetry or network feature with `SECURITY.md` (default off, no campaign payload).
