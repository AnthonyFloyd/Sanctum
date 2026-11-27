# Plans

Narrative index for planning. Executable plans live under `exec-plans/` so they can be created, moved, and reviewed independently.

## Active work

- `exec-plans/active/` — add markdown files here for in-progress multi-step work.

## Roadmap (themes)

Drawn from the shipped product surface; refine with concrete plans when starting large work.

- **Now:** Maintain local-first DM workflows (combat, maps, import/export, LAN player view); keep packaging leak-closed.
- **Next:** Automated tests for LAN guard + critical schema/API invariants; CI lint on PRs; Windows release job in GitHub Actions.
- **Later:** Code signing / notarization; deeper a11y; any multi-device DM features only with a new threat model (see `SECURITY.md`).

## Completed milestones

After a plan is fully implemented, move it from `exec-plans/active/` to `exec-plans/completed/` and optionally add a one-line summary here.

Shipped product history for humans: repo-root `docs/devlog/` and itch release notes.

## Related

- **Technical debt:** `exec-plans/tech-debt-tracker.md`
- **Product requirements:** `product-specs/`
- **Architecture:** `ARCHITECTURE.md`
