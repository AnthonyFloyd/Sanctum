# Frontend (UI) considerations

Conventions for Sanctum's web/desktop UI (Next.js App Router + React 19).

## Stack

- **Framework:** Next.js 16 App Router, React 19, TypeScript.
- **Styling:** Tailwind CSS v4 + CSS variables in `src/app/globals.css` driven by `data-theme` / `data-mode`.
- **Components:** shadcn/ui (Radix) under `src/components/ui/`; feature UI under `src/components/<domain>/`.
- **State:** Mostly server-backed fetch + local React state; no global Redux-style store. Search index is server-side (`src/lib/search/`).
- **Routing:** File-based App Router — campaign pages under `src/app/campaign/[id]/`, player `src/app/screen/[id]`, popouts under `src/app/popout/`.
- **Canvas / maps:** Konva (`react-konva`) for battle maps; Leaflet for atlas; React Flow (`@xyflow/react`) for mind maps; `@dnd-kit` for sortable combat order.

## Design system

- **Source of truth:** In-repo themes (`src/lib/themes.ts`) and `globals.css` tokens — Candlelight (default), Parchment, Arcane, Forest, Crimson, High Contrast.
- **Fonts:** Loaded in root layout (display + body); prefer existing type setup over new default stacks.
- **Shared chrome:** Campaign layout (`src/app/campaign/[id]/layout.tsx`) hosts nav, command palette (⌘K / Ctrl+K), dice roller, soundpad, tour hooks.
- **Feedback:** `sonner` toasts; autosave "Saved" indicators on long-text surfaces.

## Patterns

- **Data fetching:** Client helpers in `src/lib/client.ts` talking to `/api/*`. Prefer existing route shapes and Zod-validated bodies.
- **Forms / dialogs:** Dialogs with unsaved typed text should not close on accidental outside click (existing pattern — preserve it).
- **Loading / empty / error:** Match sibling pages in the same feature folder; keep empty states actionable (create / import / sample).
- **Heavy client-only views:** Battle map and similar use `next/dynamic` with `ssr: false` where needed; LAN phones need `allowedDevOrigins` in dev so chunks load.
- **i18n:** Dictionaries in `src/lib/i18n/` (`en|de|fr|es|it|pt.json`); cookie `sanctum_locale`; provider `src/components/i18n/i18n-provider.tsx`. Add keys to **all** locale files when introducing UI strings. Rules text stays English.
- **Theme cookie:** `sanctum_theme` — server reads for first paint (`theme-server.ts`).

## Accessibility & i18n

- **Target:** Practical keyboard use for combat and command palette; High Contrast theme for readability. Improve a11y when touching UI rather than regressing it.
- **Localization:** Chrome only; generators' word lists are English.

## Browser / platform support

- Desktop: Windows, Linux, macOS via Electron builds.
- Player view: modern mobile browsers on the same LAN.
- Responsive: DM UI is desktop-first; player screen should remain usable on phones.

## Anti-patterns to avoid

- Sending full monster HP, hidden tokens, or unrevealed fog to `/api/screen/*`.
- Putting uploads into `public/` (use `UPLOADS_DIR` + uploads route).
- Storing theme/locale only in `localStorage` (breaks across Electron ports).
- New purple-glow / generic dashboard chrome that ignores existing theme tokens.
- Introducing a second i18n library or CSS framework without a design decision.
- Cards-for-decoration in marketing-style surfaces that fight the established app chrome (follow existing campaign UI patterns inside the product).
