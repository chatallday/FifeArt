# SYSTEM PROTOCOL — FifeArt2 (React + Vite + Sanity)

## SCOPE RESTRICTION
- Only operate within `...\FifeArt website 26jul26\fifeart2-github`. Sibling `studio-fifeart` only when explicitly approved. No new GitHub/Vercel projects. No browser automation.

## GIT REMOTES (as of 18nov26)
- `origin` → https://github.com/chatallday/FifeArt.git (account chatalldaybot@gmail.com) — primary push target.
- `dynamicmike` → https://github.com/dynamicmike-dashboard/fifeart2.git (previous origin, retained for reference).
- `upstream` → https://github.com/realaicasa/fifeart2.git (original upstream, retained for reference).
- Do not create new repos; push to `origin` only. Credentials live in the local remote URL (not committed).

## STACK
- App: React 19 + Vite 6 + TS + Tailwind. Static hosting (Vercel). No backend.
- CMS: Sanity project `s2an63e9`, dataset `production`. Studio: sibling `studio-fifeart` (artwork + about schemas).

## ENV (Vite, baked at build → redeploy after changes)
- `VITE_SANITY_PROJECT_ID=s2an63e9`, `VITE_SANITY_DATASET=production` (hardcoded as defaults in `src/services/sanity.ts`)
- `VITE_SANITY_API_TOKEN` optional (private datasets only)
- `VITE_SANITY_WRITE_TOKEN` optional (admin About saves/uploads; ships in public JS — rotate if abused)
- `VITE_ADMIN_PASSWORD` for admin portal (falls back to `Tiffany201800` if unset; ships in public JS — rotate if abused)

## CONVENTIONS
- Sanity is source of truth. localStorage (`fifeart_catalog_v1`) is offline cache only.
- NEVER auto-seed `INITIAL_ARTWORKS` placeholders (`storage.ts` returns `[]` when empty).
- Migration dedupe key: `teable-<CSV id column>` (NOT the `order` column — it repeats). Script: `scripts/migrate-to-sanity.ts` (`CSV_PATH`, `LIMIT`/`OFFSET`, `WIPE_EXISTING=true` to rebuild).
- About page: Sanity singleton `_id=site-about`; admin edits + portrait upload via write token; seed: `scripts/seed-about.ts`.
- Sanity image URLs are permanent CDN links. Teable presigned URLs expire in ~6 days.

## PENDING
- Session 18nov26: GitHub push target moved to chatallday/FifeArt (origin). Admin password hardened: default fallback changed to `Tiffany201800` (env var `VITE_ADMIN_PASSWORD` still takes precedence); removed public password leak in login notice and error message. Removed 12h persisted admin session (password now required on every portal open); legacy session key cleared on mount to invalidate stale auto-auth. Commits `0820c3c`, `86ebc3a` pushed to origin/main. Typecheck + build clean.
- Session 17sep26: fixed admin logout-on-upload (localStorage 12h session + Sign out btn), white-screen crash (malformed-entry guards in storage/App/AdminPortal; guaranteed Sanity create id), memory-safe WebP conversion. Commits `6bb30e7`, `e4c828a` pushed to origin/main, live on Vercel. Price display verified faithful (31/3196 were typed values, confirmed via Sanity query).
- Catalog has duplicates from import tests (7x "Magnum P.I.", 2x "Koi") — user may request cleanup/dupe-finder.
- Offered: price-field confirm safeguard in admin form (not yet requested).
- User runs `npm run deploy` in studio-fifeart (CLI hangs in agent env).
- CORS origins in sanity.io/manage: fifeart.com, www, localhost:3000 (credentials ON).
- Vercel: add `VITE_SANITY_WRITE_TOKEN` (Editor) + redeploy to activate admin publish. `PUBLIC_SANITY_*` vars are unused — `PUBLIC_SANITY_DATASET=fifeart2` is wrong, safe to delete. Verified live bundle once lacked baked project ID.
- Rotate exposed tokens (Teable PAT + Sanity write token pasted in chat).
- Optional: delete stray untracked `fifeart2-github/fifeart/` nested studio scaffold.
