# PLANR ✦ — Local Setup & Play Store Guide

This package contains the complete PLANR app — everything runs from a single HTML file, no build step, no dependencies to install.

## What's in this folder

```
app/
├── index.html          ← The entire app (HTML + CSS + JS in one file)
├── manifest.json        ← PWA manifest (makes the app installable)
└── icons/                ← App icons, 8 sizes (72px to 512px)
```

---

## Part 1 — Run it locally right now

You don't need a server for basic testing. Just open the file:

**Easiest option:**
Double-click `app/index.html` — it opens directly in your browser. Most features work this way (tasks, habits, schedule, mood, profile). A couple of things need a real server instead of `file://` to work correctly: the PWA install prompt, notifications, and the service worker. See below if you want those too.

**To get the full PWA experience (installable, offline-capable):**
You need to serve the folder over `http://` or `https://`, not open it directly as a file. Pick any of these — all free, all take under a minute:

### Option A — Python (already on most computers)
```bash
cd app
python3 -m http.server 8080
```
Then open `http://localhost:8080` in Chrome.

### Option B — Node.js
```bash
npx serve app
```
Follow the URL it prints.

### Option C — VS Code "Live Server" extension
Right-click `index.html` → "Open with Live Server".

Once it's running on `localhost`, open it in Chrome on your phone (same WiFi network, use your computer's local IP instead of `localhost`) to test the install banner and mobile layout properly.

---

## Part 2 — Publish to the Google Play Store

**Important: I cannot generate a signed `.aab` file for you directly.** That file requires Android build tools and, critically, a signing key — and that key must be generated and held by you alone. If anyone else generates it on your behalf, you could permanently lose the ability to push updates to your own app later. What follows is the fastest real path, and it takes about 10–15 minutes.

### Step 1 — Host the app on a real domain (not localhost)
Play Store needs your app reachable on the public internet. Pick one (all free):
- **Netlify**: drag the `app` folder onto netlify.com — get a URL instantly
- **Vercel**: `npx vercel` from inside the `app` folder
- **Firebase Hosting**: `firebase deploy` (if you already have a Firebase project from earlier)
- **GitHub Pages**: push `app/` contents to a repo, enable Pages in settings

You'll end up with something like `https://planr-app.netlify.app`.

### Step 2 — Install Bubblewrap (Google's official PWA-to-Android tool)
```bash
npm install -g @bubblewrap/cli
```

### Step 3 — Generate the Android project
```bash
mkdir planr-android && cd planr-android
bubblewrap init --manifest https://YOUR-DOMAIN-HERE/manifest.json
```

It will ask you a series of questions:
- **Package name**: e.g. `com.yourname.planr` (must be unique, can't be changed later)
- **App name**: PLANR
- **Signing key**: choose "Create new key" — **this is the key only you should hold.** Bubblewrap will ask for a password; write it down somewhere safe. Losing this key means losing the ability to update your app on Play Store forever under that listing.

### Step 4 — Build the signed bundle
```bash
bubblewrap build
```

This produces `app-release-bundle.aab` — **this is the file you upload to Play Console.**

### Step 5 — Submit to Play Store
1. Go to [play.google.com/console](https://play.google.com/console) (one-time $25 registration fee if you haven't already)
2. Create a new app → Production (or Internal Testing first, recommended)
3. Upload `app-release-bundle.aab`
4. Fill in store listing: description, screenshots (take these from your phone once installed), content rating (Everyone), category (Productivity)
5. Submit for review

### Even simpler alternative: PWABuilder (no command line)
If the CLI steps feel like a lot:
1. Go to [pwabuilder.com](https://www.pwabuilder.com)
2. Enter your hosted URL (from Step 1)
3. Click "Package for Stores" → Android
4. It generates the signed `.aab` for you in-browser, still asking you to create/hold your own signing key
5. Download and upload directly to Play Console

This is genuinely the easiest route if Bubblewrap's CLI feels unfamiliar — same end result, friendlier interface.

---

## A note on your data

Right now, PLANR stores everything (tasks, habits, mood history, profile) in your browser's local storage on whatever device you're using. That means:
- Clearing your browser cache or uninstalling the app **will erase all your data**
- Data does **not** sync between devices

If you want real accounts with cloud backup (so your data survives a reinstall or works across devices), that requires wiring up Firebase — a separate, bigger step we can pick back up whenever you're ready.
