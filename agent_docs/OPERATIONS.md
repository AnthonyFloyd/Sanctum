# Operations

Deployment and day-two operations for a **local-first** app (no multi-tenant cloud). Long release notes live under repo-root `docs/`.

## Environments

| Environment | Purpose | How |
|-------------|---------|-----|
| Local web | Day-to-day development | `npm install` → `npm run dev` → http://localhost:3000 |
| Local production build | Verify Next standalone behavior | `npm run build` → `npm start` (or `npm run start:lan`) |
| Packaged desktop | End-user distribution | `npm run desktop:build` / `:win` / `:linux` / `:mac` |
| Release CI | Linux + macOS artifacts | Tag `v*` or `workflow_dispatch` → `.github/workflows/release-builds.yml` |

Windows desktop builds are still produced locally (`desktop:build:win`); CI comment in the workflow notes adding `windows-latest` later.

## Configuration

| Variable / file | Purpose |
|-----------------|---------|
| `SANCTUM_DATA_ROOT` | Writable root (Electron sets userData) |
| `SANCTUM_MIGRATIONS_DIR` | Bundled Drizzle migrations in desktop builds |
| `HOSTNAME` | Bind address (`0.0.0.0` when LAN sharing enabled in Electron) |
| `lan.json` | `{ enabled, shareToken, … }` beside data root |
| Theme / locale cookies | `sanctum_theme`, `sanctum_locale` |

Document **names**, never commit values that are secrets. `BUTLER_API_KEY` is CI-only for itch push.

## Scripts (operator cheat sheet)

| Script | Use |
|--------|-----|
| `npm run dev` | Hot reload (copies pdf worker + leaflet in `predev`) |
| `npm run build` / `npm start` | Production Next |
| `npm run start:lan` | Production bound to all interfaces |
| `npm run lint` | ESLint |
| `npm run db:generate` | New migration after schema edits |
| `npm run db:migrate` | Apply migrations manually |
| `npm run db:studio` | Drizzle Studio |
| `npm run desktop:build*` | Package Electron app for current/target OS |

## Backups & disaster recovery

- **Automatic:** On DB open, daily snapshot under `backups/` plus a `backups` table row (`src/db/index.ts`, `src/lib/backup.ts`).
- **Manual:** Settings → export campaign JSON; or copy the whole `data/` (and preferably `uploads/` + `lan.json`) directory.
- **Desktop locations:** Windows `%APPDATA%\Sanctum`; macOS `~/Library/Application Support/Sanctum`; Linux `~/.config/Sanctum`.
- **Restore:** Replace files while the app is quit; or import campaign JSON into a running instance.

## Release runbook (pointer)

1. Bump version in `package.json`; update changelog / What's new as existing process expects.
2. Tag `vX.Y.Z` (triggers Linux/macOS CI) and/or run workflow by hand.
3. Build Windows locally; attach or push to itch per `docs/ITCH_RELEASE.md`.
4. Smoke LAN guard and leak checks from CI; unsigned macOS/Windows need first-launch Gatekeeper/SmartScreen steps (documented in README).

## Runbooks & on-call

- Single-maintainer project: no pager rotation.
- User-facing ops issues: data location, LAN sharing restart, firewall helper (`src/lib/lan.ts` `allowThroughFirewall` on Windows).
