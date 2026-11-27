# Database schema (generated)

Human-readable snapshot of Sanctum's SQLite tables. **Source of truth:** `src/db/schema.ts` and SQL under `drizzle/`.

**Last updated:** 2026-11-26

---

## How to update

After meaningful schema or migration changes, refresh this file from `schema.ts` (table list + notes) and bump the date above.

## Schema

Engine: SQLite via `better-sqlite3` + Drizzle. WAL mode; foreign keys on. DB path: `{DATA_ROOT}/data/sanctum.db`.

### Core

| Table | Purpose |
|-------|---------|
| `campaigns` | Campaign root (name, description, rulebooks JSON, calendar config, …) |
| `app_settings` | App-wide KV settings |
| `backups` | Metadata rows for backup snapshots |

### Party / sheets

| Table | Purpose |
|-------|---------|
| `characters` | PC sheets (abilities, HP, features, currency JSON, portrait, …) |
| `character_resources` | Limited-use abilities (recharge on rest) |
| `spells` | Spells owned by character or NPC (`owner_id` + `owner_type`) |
| `spell_slots` | Slot tracking per owner |
| `inventory_items` | Inventory per owner |

### NPCs / bestiary

| Table | Purpose |
|-------|---------|
| `npcs` | NPC / monster templates and instances |
| `npc_relationships` | Edges between NPCs (ally, enemy, family, …) |

### Combat

| Table | Purpose |
|-------|---------|
| `encounters` | Saved / active encounters |
| `combat_participants` | Initiative rows (HP, conditions, legendary, minion groups, …) |
| `combat_log_entries` | Per-round combat log |
| `battle_maps` | Grid map, tokens JSON, fog JSON, annotations JSON |

### World / atlas

| Table | Purpose |
|-------|---------|
| `locations` | World locations (notes, random encounter tables, …) |
| `atlas_maps` | Zoomable atlas map images / config |
| `atlas_categories` | Marker categories |
| `atlas_markers` | Markers linked to campaign records / portals |

### Story / prep

| Table | Purpose |
|-------|---------|
| `story_events` | Timeline events / threads / milestones |
| `factions` | Factions |
| `calendar_events` | Calendar entries |
| `session_preps` | Session prep (plot points, planned encounters) |
| `notes` | Sticky / freeform notes |
| `quests` | Quests + sub-objectives JSON |
| `rumors` | Rumors / gossip |
| `clocks` | Progress clocks |
| `handouts` | Player-facing Markdown handouts |

### Content / extras

| Table | Purpose |
|-------|---------|
| `documents` | Stored documents / PDF references |
| `homebrew_spells` | DM-authored spells |
| `homebrew_feats` | DM-authored feats |
| `magic_items` | Magic item catalogue / assignments |
| `shops` | Shops |
| `shop_items` | Shop stock |
| `roll_tables` | Weighted roll tables |
| `generator_lists` | Per-campaign custom generator word lists |
| `mind_maps` | Mind map graphs JSON |
| `sound_assets` | Soundpad audio files metadata |
| `soundscapes` | Saved layered soundscapes |

### Shared patterns

- Most domain tables include `campaign_id` and timestamp columns.
- JSON-typed text columns use Drizzle `mode: "json"` with typed `$type<…>()`.
- Polymorphic children (`spells`, `spell_slots`, `inventory_items`): no SQL FK to owners; app deletes via `cascadeOwned`.

## Related

- Conventions: `../DATA.md`
- JSON default pitfall: `../../docs/JSON_COLUMN_DEFAULTS_FIX.md`
