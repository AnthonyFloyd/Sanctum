# New user onboarding

How a **new DM** discovers, starts, and succeeds with Sanctum. Written for product behavior; implementation hooks live in `src/lib/onboarding.ts` and related UI.

## First-time experience

- **Entry points:** itch.io desktop download (Win/Linux/macOS) or run from source (`npm run dev`).
- **Account creation:** None. First launch creates `sanctum.db` and applies migrations automatically.
- **Welcome:** Shown only when `hasSeenWelcome` is unset **and** there are zero campaigns (`welcomeEligible()`). Upgrades that already have campaigns mark welcome seen quietly.
- **First success:** Either take the guided tour on the Sample Campaign (*The Lost Souls*) or create a campaign and open a character / start combat.

## Core flows (happy path)

1. Install or `npm install && npm run dev`.
2. See welcome → **Take the tour** (loads/reopens sample) or **Create a campaign**.
3. Tour spotlights: character sheets, combat tracker, battle map, atlas, generators, ⌘K search (read-only; does not mutate beyond opening the sample).
4. Prep: add party characters (wizard or PDF import), NPCs, locations; optionally enable a theme/locale in Settings.
5. Session: run combat, reveal battle map / handouts / clocks to player view; optional Wi-Fi sharing via Settings QR.
6. Afterward: daily backup already on disk; export campaign JSON if sharing a backup copy.

## Guidance & help

- **In-product:** Welcome dialog, guided tour, Help button (bottom right) to retake tour, What's new after version bumps (never stacked with welcome).
- **Docs:** README (features, LAN sharing, data paths); itch page for downloads.
- **Support:** Maintainer contact via project channels (itch/GitHub as published).

## Terminology

| Term | Meaning |
|------|---------|
| Campaign | Top-level container for all table data |
| Player view | Read-only (mostly) second screen at `/screen/[id]` |
| Share link / QR | LAN URL carrying a random token; regenerated from Settings |
| Sample Campaign | Built-in English demo (*The Lost Souls*); normal campaign row, reloadable |
| Fog of war | Cell mask on battle/atlas maps; unrevealed data not sent to players |
| Homebrew | DM-authored spells/feats/etc. marked `source = "homebrew"` |
| Command palette | ⌘K / Ctrl+K search and quick actions |

## Accessibility & inclusivity

- High Contrast theme follows system light/dark.
- UI translations: English, German, French, Spanish, Italian, Portuguese.
- Sample and generator word lists are English; rules reference remains English.
- Combat keyboard shortcuts and cheat-sheet (`?`) support keyboard-driven play.
