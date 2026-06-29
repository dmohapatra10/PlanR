# PLANR ✦ — Local Setup & Play Store Guide

This package contains the complete, current version of PLANR — everything runs from a single HTML file, no build step, no dependencies to install.

## What's in this folder

```
app/
├── index.html      ← The entire app (HTML + CSS + JS in one file)
├── manifest.json   ← PWA manifest (makes the app installable)
└── icons/          ← App icons, 8 sizes (72px to 512px)
```

## What's included in this version

- Onboarding: name, age, height, weight, a personal goal, and goal-specific habit suggestions (11 per goal) with editable From/To reminder times
- Today screen: tasks, linked to habits where relevant, with a daily mood check-in
- Habits screen: task-driven progress bars, weekly history, editable reminders
- Schedule screen: time-blocked timeline with live "in progress" highlighting
- Profile screen: your onboarding info, BMI, mood trend, achievements
- Local data persistence (saved in your browser only — see note at the bottom)
- Installable as a PWA with offline support

---

## Part 1 — Run it locally right now

**Easiest option:** Double-click `app/index.html` to open it directly in your browser. Most features work this way. A few things need a real server instead of `file://`: the PWA install prompt, notifications, and the service worker.

**To get the full PWA experience:**

### Option A — Python (already on most computers)
```bash
cd app
python3 -m http.server 8080
```
Open `http://localhost:8080`.

### Option B — Node.js
```bash
npx serve app
```

### Option C — VS Code "Live Server" extension
Right-click `index.html` → "Open with Live Server".

---

## Part 2 — Host it publicly (needed before Play Store packaging)

**GitHub Pages** (free):
1. Create a public GitHub repo
2. Upload the contents of the `app` folder to the repo root (not nested in a subfolder — `index.html` should sit at the top level)
3. Settings → Pages → Source: Deploy from branch → `main` → `/ (root)` → Save
4. Your live URL: `https://YOUR_USERNAME.github.io/REPO_NAME/`

Netlify, Vercel, and Firebase Hosting all work too — drag-and-drop or one CLI command, depending on the service.

---

## Part 3 — Publish to the Google Play Store

**I cannot generate a signed `.aab` file directly** — that requires a signing key that only you should ever generate and hold, since Play Store permanently ties your app's identity to it.

### Fastest path: PWABuilder (no command line)
1. Go to [pwabuilder.com](https://www.pwabuilder.com)
2. Enter your hosted URL (from Part 2)
3. Click "Package for Stores" → Android
4. Fill in package ID (e.g. `com.yourname.planr` — permanent, choose carefully), app name, version
5. Choose "Create new signing key" — **save the generated keystore file and its password somewhere permanent.** Losing it means you can never update this app listing again.
6. Download → you'll get `app-release-bundle.aab` plus your keystore file

### Alternative: Bubblewrap CLI
```bash
npm install -g @bubblewrap/cli
mkdir planr-android && cd planr-android
bubblewrap init --manifest https://YOUR-HOSTED-URL/manifest.json
bubblewrap build
```
Produces the same kind of signed `.aab`, with the same signing-key responsibility on your end.

### Submitting
1. [play.google.com/console](https://play.google.com/console) — $25 one-time developer fee if you haven't registered yet
2. Create app → Internal Testing (recommended first) or Production
3. Upload the `.aab`
4. Fill in store listing: description, screenshots, content rating (Everyone), category (Productivity)
5. Submit for review

---

## A note on your data

PLANR currently stores everything (tasks, habits, mood history, profile) in your browser's local storage on whatever device you're using. Clearing your browser cache or uninstalling the app will erase all your data, and data does not sync between devices. Real cloud accounts (so data survives a reinstall or syncs across devices) would require a separate Firebase integration — a bigger step we can tackle whenever you're ready.
