# Sidak's Tracker

A standalone, offline-capable workout tracker — a 6-day push/pull/legs hypertrophy
split with a set-by-set checklist, a real calendar, and a weekly progress chart.
No account, no backend, no Claude runtime: it's a plain static site. Your logged
sets are saved in the browser's `localStorage` on whatever device opens it, and
nowhere else.

The top of the page shows a small stat header: a progress ring for today, plus
longest streak / weekly consistency / training days / perfect-days-this-week
cards, and a 7-day week strip you can tap to jump to any day. All of it is
computed live from the same `localStorage` data as the daily checklist below —
nothing new to sync or configure.

## Files

- `index.html` — the whole app.
- `manifest.json` / `icon.svg` / `service-worker.js` — make it installable as a
  home-screen app and let it load offline once visited once.

## Run it locally

Open `index.html` directly in a browser, or serve the folder so the service
worker can register properly:

```bash
npx serve .
```

## Deploy (GitHub Pages)

1. Push this folder to a GitHub repo.
2. Repo Settings → Pages → Deploy from branch → `main` / `/ (root)`.
3. Your app is live at `https://<username>.github.io/<repo>/`.

## Edit the plan

The whole week lives in the `PLAN` array at the top of the `<script>` block in
`index.html` — each day has `warmup`, `main`, `core`, and `cooldown` arrays of
`[exercise name, sets, reps]`. Edit it directly and redeploy (or just refresh
if you're running it locally) to change the program.
