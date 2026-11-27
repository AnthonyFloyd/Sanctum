# Product requirements (user-specified)

Aligned with root `README.md`, `DESIGN.md`, and `new-user-onboarding.md`.

## Document control

- **Owner:** Maintainer (athanasioust)
- **Last updated:** 2026-11-26
- **Status:** approved (reflects shipped v1.x behavior)

## Overview

- **Problem statement:** DMs need an offline campaign manager that covers prep and live play (sheets, combat, maps, world, handouts) without forcing a cloud account or AI dependency.
- **Target users:** Solo Dungeon Masters running D&D 5e (2014-oriented rules support with some 2024 weapon mastery), on Windows/Linux/macOS.
- **Out of scope:** Hosted multiplayer DM collaboration, accounts, cloud sync, AI content generation, exposing the full DM app to other devices.

## Functional requirements

| ID | Requirement | Priority | Notes |
|----|-------------|----------|-------|
| FR-001 | Create/manage campaigns with characters, NPCs, locations, quests, factions, notes, etc. | Must | SQLite-backed |
| FR-002 | Full 5e character sheets: abilities, skills, HP, spells, inventory, currency, hit dice/rests, limited-use resources, portrait tokens | Must | Wizard + level-up |
| FR-003 | Bestiary/NPC templates; import 140+ SRD monsters | Must | Bundled SRD-derived data |
| FR-004 | Combat tracker: initiative, conditions, death saves, concentration, combat log, keyboard shortcuts, pop-out | Must | Boss/minion/acted features |
| FR-005 | Battle map with tokens, AoE, fog of war, optional player token drag | Must | Konva; fog filtered for players |
| FR-006 | World atlas (offline Leaflet), markers, nested maps, player fog, HTML export | Must | |
| FR-007 | Player view for TV/phones with share link + QR; regenerate invalidates old links | Must | LAN guard |
| FR-008 | Dice roller with contextual modifiers and notation | Must | |
| FR-009 | Quests, magic items, handouts, rumors, clocks, shops, roll tables, calendar, sessions, story timeline | Must | |
| FR-010 | Mind map, soundpad, homebrew spells/items/monsters/feats, PDF viewer | Should | |
| FR-011 | Generators (NPC, tavern, treasure, …) with custom lists; save into campaign | Should | Offline, English lists |
| FR-012 | ⌘K / Ctrl+K search across campaign records + quick commands | Must | |
| FR-013 | Markdown/Obsidian import (one-way) and fillable character PDF import/export | Should | |
| FR-014 | Campaign JSON export/import; automatic daily backups | Must | |
| FR-015 | Themes + UI locales (en/de/fr/es/it/pt) | Should | Rules text English |
| FR-016 | Welcome, guided tour, sample campaign, What's new | Should | |
| FR-017 | Opt-in update check via GitHub Releases only | Could | Default off |

## Non-functional requirements

| ID | Category | Requirement | Notes |
|----|----------|-------------|-------|
| NFR-001 | Privacy | No account; no campaign data sent to the network by default | Update check is opt-in and metadata-only |
| NFR-002 | Security | Other LAN devices never get DM APIs; player payload is filtered | `scripts/lan-guard.cjs` |
| NFR-003 | Portability | Desktop builds for Win/Linux/macOS; data in per-user dir when packaged | Electron |
| NFR-004 | Offline | Core features work without internet | Generators/atlas/combat included |
| NFR-005 | License | AGPL-3.0-only; preserve attribution | `LICENSE` |
| NFR-006 | Performance | Long lists remain usable; virtualize where already established | |

## Assumptions & dependencies

- Node 22.14+ for source builds; native toolchain only if `better-sqlite3` lacks a prebuild.
- Players and DM share a local network when using phone player view.
- SRD content licensing remains CC-BY attribution as documented in README.

## Open questions

- Priority and shape of first automated test suite (see tech-debt tracker).
- Code signing budget for reducing first-launch OS warnings.
