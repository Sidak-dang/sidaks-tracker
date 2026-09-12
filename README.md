# Sidak's Tracker

A standalone, offline-capable workout tracker — a 6-day push/pull/legs hypertrophy
split with a set-by-set checklist, a real calendar, and a weekly progress chart.
It's a plain static site with no build step. Your logged sets are always saved
to this device's `localStorage` first, so the app is fully usable offline —
and if you've set up cloud sync (see below), they're also synced to your own
private Firebase project so the same data follows you across devices.

The top of the page shows a small stat header: a progress ring for today, plus
longest streak / weekly consistency / training days / perfect-days-this-week
cards, and a 7-day week strip you can tap to jump to any day. All of it is
computed live from the same `localStorage` data as the daily checklist below —
nothing new to sync or configure.

## Files

- `index.html` — the whole app, including the optional cloud sync code.
- `manifest.json` / `icon.svg` / `service-worker.js` — make it installable as a
  home-screen app and let it load offline once visited once.
- `firestore.rules.txt` — security rules to paste into your Firebase project
  (not deployed automatically — see setup below).

## Run it locally

Serve the folder rather than opening `index.html` directly — the cloud sync
code is a JS module, and browsers block module imports from a bare `file://`
page:

```bash
npx serve .
```

## Cloud sync setup (one-time, per person/project)

This app now has an email/password login gate. Signed-in data syncs to a
Firestore database in your own free Firebase project, so opening the app on
a new device and signing in pulls your existing data down automatically.
There's no sign-up screen on purpose — you create the one account for the
project by hand in the console, so no stranger can self-register.

1. Go to the [Firebase console](https://console.firebase.google.com), create
   a new project (no credit card needed for the free Spark plan).
2. **Project settings -> General -> Your apps -> add a Web app.** Copy the
   `firebaseConfig` object it gives you.
3. In `index.html`, find `const FIREBASE_CONFIG = { ... }` near the bottom
   (inside the last `<script type="module">` block) and paste your values
   in, replacing the `"REPLACE_ME"` placeholders.
4. **Build -> Authentication -> Sign-in method -> enable "Email/Password".**
5. **Build -> Authentication -> Users -> Add user** — create the one login
   (your email + a password) for this project.
6. **Build -> Firestore Database -> Create database** (production mode, any
   nearby region).
7. **Firestore Database -> Rules** — paste in the contents of
   `firestore.rules.txt` and Publish.
8. Deploy/redeploy as usual (see below) and open the app — you'll land on a
   sign-in screen. Sign in with the account from step 5.

If you skip this setup, the app detects the unfilled `REPLACE_ME` config and
runs exactly as before — no login gate, local-only storage, no errors.

## Deploy (GitHub Pages)

1. Push this folder to a GitHub repo.
2. Repo Settings → Pages → Deploy from branch → `main` / `/ (root)`.
3. Your app is live at `https://<username>.github.io/<repo>/`.

## Edit the plan

The whole week lives in the `PLAN` array at the top of the `<script>` block in
`index.html` — each day has `warmup`, `main`, `core`, and `cooldown` arrays.
`main` and `core` rows are `[exercise name, sets, reps, RPE]`; `warmup` and
`cooldown` rows are `[exercise name, sets, reps]` (no RPE, since mobility/
stretch work isn't logged at an intensity). The 4th field is optional —
`accSection()` in `index.html` only renders it when present, so a row without
one just shows `sets×reps` as before. Edit the array directly and redeploy
(or just refresh if you're running it locally) to change the program.
