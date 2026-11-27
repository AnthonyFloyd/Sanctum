# Security

**Non-negotiables** for Sanctum development and packaging. Update when the threat model or LAN rules change.

## Threat model (summary)

- **Assets:** Campaign SQLite DB, uploads (portraits, maps, PDFs, audio), share token in `lan.json`, anything the DM typed into the app.
- **Adversaries:** Other devices on the same Wi-Fi when LAN sharing is on; accidental exposure of the full DM API; packaging leaks of local `uploads/` or source into the desktop bundle; supply-chain dependency risk.
- **Trust boundaries:** Loopback (full DM trust) vs LAN clients (player view only). The guard runs **before** Next and does not trust `X-Forwarded-For` or similar client headers for authorization.

## Local-first defaults

- No accounts, no cloud sync, no telemetry by default.
- Opt-in **update check** only: one unauthenticated GitHub Releases request; nothing about the user or campaigns is sent (`src/lib/update-check.ts`).
- Writable data stays under the data root (`src/lib/paths.ts`); never commit `data/`, `backups/`, `uploads/`, or `lan.json`.

## Secrets & configuration

- **No secrets in git.** Desktop itch push uses CI secret `BUTLER_API_KEY` when present.
- Share token is **not** a user password: 128-bit random value in `lan.json`, rotated via Settings → Regenerate link.
- Do not log share tokens, cookies, or full campaign dumps in error output.

## LAN player-view guard

Implementation: `scripts/lan-guard.cjs` (`install`, `decide`, `gate`). Config helpers: `src/lib/lan.ts`. Settings UI: `src/components/settings/player-view-sharing.tsx`.

| Client | Sharing | Allowed |
|--------|---------|---------|
| Loopback | any | Full app (no token) |
| Non-loopback | off | Nothing |
| Non-loopback | on | Static assets without data; `/screen/*`, `/api/screen/*`, `/uploads/*` **with** valid share token/cookie |

Rules of note:

- First visit: `?token=` → verify → HttpOnly `sanctum_share` cookie → redirect to clean URL.
- Regenerating the token invalidates every old link and cookie.
- Remote writes: only `/api/screen/*` (e.g. player token move when enabled). Server Actions (`next-action`) from remotes are denied.
- Player state is filtered server-side (`src/lib/screen/state.ts`): hidden content and unrevealed fog are not sent; monsters show Healthy / Bloodied / Down, not exact HP.
- Binding to `0.0.0.0` requires a desktop restart after enabling sharing; token regenerate and `allowPlayerTokenMoves` apply immediately.
- With `npm start` / `npm run dev`, the guard loads via `next.config.ts`, so the earliest moments of startup may not be covered; prefer the desktop app when that matters (documented in README).

## Application security

- **Authentication:** None for the DM app (single-user local). Remotes are gated by the share token, not user accounts.
- **Authorization:** Network path + token at the guard; additional feature flags (e.g. player token moves) in LAN/settings state.
- **Input validation:** Zod schemas in `src/lib/validators.ts`; `parseBody` / `apiRoute` in `src/lib/api.ts`. Prefer server-side validation on every mutating route.
- **File uploads:** Stored under `UPLOADS_DIR` and served via `src/app/uploads/[filename]/route.ts`, not from `public/`.
- **Output encoding:** React's default escaping; Markdown rendering must stay XSS-safe for DM-authored and revealed content.
- **CSRF / CORS:** Not a multi-origin SaaS. Do not weaken the LAN guard or add permissive CORS for DM APIs.
- **Dependencies:** Review lockfile changes; keep `better-sqlite3` as `serverExternalPackages`.

## Packaging & supply chain

- `next.config.ts` `outputFileTracingExcludes` and `scripts/build-desktop.mjs` fail closed so `src/`, `docs/`, `uploads/`, `.git`, etc. do not ship in the desktop bundle.
- Release workflow (`.github/workflows/release-builds.yml`) includes leak / LAN-guard checks for Linux and macOS artifacts.
- Pin and review dependency upgrades that touch Electron, Next, or native modules.

## Data protection

- **At rest:** OS filesystem permissions on the user data directory; SQLite file is local plaintext (treat the machine as the trust boundary).
- **In transit:** LAN player view is typically HTTP on the local network. Do not claim end-to-end encryption; document that sharing is same-Wi-Fi trust.
- **PII:** Campaigns may contain real names or personal notes; export/backup files should be treated as sensitive by the DM.

## Reporting

- Prefer private disclosure to the maintainer (`athanasioust`) for security issues that could expose campaign data over LAN or leak files in builds.
