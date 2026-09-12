# Sidak's Tracker — instructions for Claude

This is a single-file static PWA. All app code (markup, styles, JS) lives in
`index.html`. There is no build step and no bundler — edit `index.html`
directly and it's ready to deploy as-is.

## Deploy flow (this is the whole thing)
1. Edit `index.html` (and/or `service-worker.js` / `manifest.json` if truly
   needed).
2. Commit and push to `main`. GitHub Pages redeploys automatically.
3. Refresh the app on the phone/browser once the deploy is live (~1-2 min).

No cache-busting step is needed for normal edits — see below for why.

## Service worker caching — do not change this without reading this section
`service-worker.js` uses a **network-first, cache-fallback** fetch strategy:
every request tries the network first and updates the cache on success. The
cache is only used as a fallback when there's no connectivity (e.g. offline
at the gym), serving whatever was last fetched successfully.

This means: a content change to `index.html` shows up automatically the next
time the page loads, as long as the phone has a network connection at that
moment. You do **not** need to manually bump the `CACHE` version string in
`service-worker.js` for ordinary edits — that used to be required back when
the fetch handler was cache-first, and forgetting to bump it is exactly what
caused stale versions to get stuck on the phone before this was fixed.

Only bump `CACHE` (e.g. `"iron-ledger-v9"` → `"iron-ledger-v10"`) if you
deliberately want to purge everything currently cached on a user's phone —
this is rare and not part of the normal edit/commit/push loop.

Do not revert the fetch handler back to cache-first (checking `caches.match`
before `fetch`) — that reintroduces the stale-cache problem this file exists
to prevent.
