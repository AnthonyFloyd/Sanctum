# Design considerations

Product and technical principles that should stay consistent across Sanctum features. Structure lives in `ARCHITECTURE.md`; this file is the *why*.

## Product principles

- **Users:** Solo Dungeon Masters running D&D 5e tables, often offline, often on a laptop plus a TV or player phones.
- **Core jobs-to-be-done:**
  - Prep and run combat (initiative, HP, conditions, battle map, log).
  - Keep campaign truth in one place (characters, NPCs, quests, locations, factions).
  - Show players only what they should see (player view + fog + share link).
  - Import existing notes/sheets and export/backup without a cloud account.
- **Success metrics (qualitative):** Time-to-first-campaign; combat usable mid-session without friction; zero surprise network egress; player phones stay read-only unless explicitly allowed.

## UX principles

- **Local and immediate:** Actions should feel like a desktop tool (autosave for long text, keyboard combat shortcuts, pop-out windows).
- **DM authority:** The server decides player visibility; UI never "hides" sensitive data only on the client for the player view.
- **Progressive discovery:** Welcome dialog, guided tour on the Sample Campaign (*The Lost Souls*), Help retake, What's new after updates (`src/lib/onboarding.ts`, tour components).
- **Accessibility:** High Contrast theme follows system light/dark; keyboard paths for combat (`?` cheat-sheet). Prefer visible focus and readable contrast in new UI.
- **Languages:** Chrome UI in en/de/fr/es/it/pt; rules/SRD text stays English.

## Technical principles

- **Simplicity:** Prefer boring patterns already in the repo (`apiRoute`, Zod validators, `collectionHandlers` / `itemHandlers`).
- **One database:** Campaign-scoped rows in SQLite; avoid introducing a second persistence store without a design doc.
- **Offline by default:** Generators, atlas, combat, and dice must work with no network. Network features are opt-in and documented.
- **Consistency:** Shared JSON shapes and column helpers live at the top of `src/db/schema.ts`. Validate API bodies with schemas in `src/lib/validators.ts`.
- **Fail closed on packaging:** Desktop bundles must not include DM files or source trees.

## Trade-offs

| Area | Choice | Rationale |
|------|--------|-----------|
| Auth | None for DM; token for LAN players | Single-user local app; auth would not buy much and would hurt offline setup |
| DB | SQLite file | Portable, zero ops, fits local-first |
| Player HP | Bands only on player view | Prevents metagaming from exact monster HP |
| Themes/locale | Cookies | Survive Electron's new port/origin each launch |
| JSON defaults | Real values, not strings | Avoid double-encoding (see `docs/JSON_COLUMN_DEFAULTS_FIX.md`) |
| Polymorphic ownership | App-level cascade | Spells/inventory owned by character or NPC without complex FKs |

## Open design questions

- Broader automated test pyramid (see `TESTING.md` and tech-debt tracker).
- Whether/when to notarize macOS or Authenticode-sign Windows builds (cost vs install friction).
