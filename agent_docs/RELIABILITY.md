# Reliability

How Sanctum stays **correct and recoverable** on a single machine. There are no multi-region SLOs; the unit of reliability is the DM's session and data files.

## Service level objectives (practical)

| Metric | Target | Measurement |
|--------|--------|-------------|
| Local open | App becomes usable after health check | Electron polls `/api/health` before showing the window |
| Data durability | Daily automatic DB backup + manual export | `backups/` + Settings export |
| LAN player view | Phones keep working while sharing stays on and token valid | Cookie + guard; regenerate is intentional break |

## Failure modes

| Failure | Behavior / mitigation |
|---------|------------------------|
| DB open / migrate error | Log and fail loudly; do not silently wipe data |
| Missing/unreadable `lan.json` | Treat as sharing off (deny remotes) |
| Invalid share token | Redirect/deny to `/screen/link-expired` |
| Windows firewall blocking bind | Optional named firewall rules via `allowThroughFirewall()` |
| Desktop port change each launch | Theme/locale cookies avoid "settings reset" |
| Upload / PDF parse failure | API returns structured error; no partial corrupt write where avoidable |
| Opt-in update check fails | Silent; never blocks the app |

## Resilience patterns

- **SQLite WAL:** Better concurrent read behavior for player polling + DM writes.
- **Singleton DB handle:** Reused across HMR via `globalThis` in `src/db/index.ts`.
- **Search staleness:** Writes mark search stale instead of risking a stale index forever.
- **Autosave:** Long-text surfaces save on debounce/blur so a crash loses less prep.
- **No cloud dependency:** Core features work offline; network features degrade to no-op when disabled.

## Observability

- **Logging:** Server `console.error` for unhandled API errors (`apiRoute`). Avoid logging tokens or full character sheets.
- **Health:** `GET /api/health` for desktop startup readiness.
- **Metrics / tracing / paging:** Not applicable to the local desktop product today.

## Incident response (pointers)

- Corrupt or upgraded DB: restore from `backups/` or campaign JSON export.
- Accidental LAN exposure: disable sharing and regenerate link immediately.
- Bloated desktop build: check tracing excludes and `scripts/build-desktop.mjs` allowlist (see `next.config.ts` comments).
- JSON field weirdness (`books.some is not a function`, blank currency): see `docs/JSON_COLUMN_DEFAULTS_FIX.md`.

## Capacity & performance

- **Assumptions:** One DM, modest party size, dozens–hundreds of NPCs/locations; virtualized long lists where built (`@tanstack/react-virtual`).
- **Levers:** Keep player payloads filtered and small; avoid shipping fog/tokens the player must not see; do not load full SRD datasets on every request if a tighter query exists.
