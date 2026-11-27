# Testing

Strategy for verification in Sanctum today, and where the gaps are.

## Principles

- **Fast feedback:** `npm run lint` locally before substantive PRs.
- **Meaningful coverage:** Prefer tests that lock LAN guard decisions, JSON column defaults, and player-view filtering once a runner exists.
- **Deterministic:** Avoid depending on wall-clock backup timing or live network in automated tests.

## Current reality

| Layer | Status | Tools / notes |
|-------|--------|----------------|
| Lint | Exists | `npm run lint` — ESLint flat config (`eslint.config.mjs`, `eslint-config-next`) |
| Unit | **None** | No Jest/Vitest scripts; no `*.test.*` / `*.spec.*` in repo |
| Integration | **None** | DB exercised manually / via app start migrations |
| E2E | **None** | No Playwright/Cypress |
| Release checks | Exists | `.github/workflows/release-builds.yml` builds Linux/macOS desktop artifacts and runs leak / LAN-guard related checks |

`scripts/lan-guard.cjs` exports `decide` for tests, but no automated suite calls it yet.

## Quality gates

- **Before merge (practical today):** Lint clean on touched areas; manually exercise the feature path; do not regress LAN rules or player filtering.
- **Before release:** Desktop build for target OS; smoke DM loginless open → campaign → combat → player view; confirm sharing off denies phones; confirm regenerate link invalidates old cookies; itch/GitHub assets as in `docs/ITCH_RELEASE.md`.

## Manual smoke checklist (high value)

1. Fresh data dir: welcome → sample campaign or create campaign.
2. Character create / level-up; JSON fields (currency, rulebooks) remain arrays/objects.
3. Combat: initiative, HP, conditions, concentration, legendary/lair if relevant.
4. Battle map fog + reveal to player view; confirm hidden content absent from network payload.
5. LAN sharing: QR/link on phone; without token → denied; regenerate → old link dead.
6. Export/import campaign JSON; daily backup folder gains a snapshot after first open of a day.

## Test data

- Prefer the built-in **Sample Campaign** and fixtures under `public/sample/` where applicable.
- Never commit real campaign DBs or uploads.
- PDF importer: use fillable sheets the developer owns; `node scripts/inspect-sheet.mjs "<sheet.pdf>"` for field dumps.

## Known gaps

- No CI lint/test on pull requests (release workflow is tag/`workflow_dispatch` only).
- LAN `decide()` matrix untested automatically.
- Schema JSON-default regression only documented in `docs/JSON_COLUMN_DEFAULTS_FIX.md`.
- Track repayment in `exec-plans/tech-debt-tracker.md`.
