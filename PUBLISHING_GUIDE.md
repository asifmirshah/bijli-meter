# Bijli Meter Tracker — install it today, and publish it later

---

## Part 1 — Use it on your phone right now (5 minutes, free)

The app is a **PWA** (Progressive Web App). It installs to your home screen, runs full-screen with no browser bars, and works with no internet at all. This is not a demo — it is the finished app.

### Step 1: put the files online
The files must be served over **HTTPS** for offline mode to work. Free options:

**GitHub Pages** (recommended, free forever)
1. Create a free account at github.com
2. New repository → name it `bijli-meter` → **Public** → Create
3. Click **Add file → Upload files**, drag in all 6 files (`index.html`, `manifest.json`, `service-worker.js`, `icon-192.png`, `icon-512.png`, `icon-1024.png`) → Commit
4. Settings → Pages → Source: **main branch** → Save
5. After ~1 minute your app is live at `https://<yourname>.github.io/bijli-meter/`

**Netlify Drop** (fastest, no account needed to try): go to `app.netlify.com/drop` and drag the folder in.

### Step 2: install it on the phone
- **Android (Chrome):** open the link → menu ⋮ → **Add to Home screen** → Install
- **iPhone (Safari — must be Safari, not Chrome):** open the link → Share button → **Add to Home Screen**

It now behaves like any installed app: own icon, full screen, works offline, data saved on the phone.

> **Testing without a server:** you can open `index.html` directly from the phone's file manager, but offline caching and the secure hash won't work properly. Use a real HTTPS link.

---

## Part 2 — Publishing to the app stores

Here is the honest picture. I can't upload it for you, and neither store accepts a folder of files — each needs its app wrapped in a native package, built on your own machine, with a paid developer account.

### Google Play — realistic, ~PKR 7,000 one-off

Google **explicitly supports** PWAs through Trusted Web Activity. This is the practical route.

**Requirements**
- Google Play Developer account: **$25 one-time**
- Node.js on your laptop
- Your app already live on HTTPS (Part 1)

**Steps**
```bash
npm install -g @bubblewrap/cli
bubblewrap init --manifest https://YOURNAME.github.io/bijli-meter/manifest.json
bubblewrap build
```
That produces `app-release-bundle.aab`. Upload it at play.google.com/console, fill in the store listing, and submit. Review usually takes a few days.

You will also need: a privacy policy URL, a 512×512 icon (included), a 1024×500 feature graphic, and at least 2 phone screenshots.

### Apple App Store — harder, be prepared

- Apple Developer Program: **$99 per year, recurring**
- **A Mac with Xcode is mandatory.** There is no way around this; you cannot build an iOS app on Windows.
- **App Store Review Guideline 4.2 (Minimum Functionality)** rejects apps that are just a website in a wrapper. A plain PWA wrapper will very likely be rejected.

To pass 4.2, the app needs genuine native capability. Realistic additions: camera OCR to read the meter dial from a photo, local notifications reminding you to take today's reading, home-screen widget showing the month's units, biometric (Face ID / fingerprint) unlock. With two or three of those, it becomes a real app rather than a wrapped page.

**Route:** use Capacitor.
```bash
npm install -g @capacitor/cli
npx cap init "Bijli Meter Tracker" com.yourname.bijlimeter
# copy index.html and friends into the www/ folder
npx cap add ios
npx cap add android
npx cap open ios      # opens Xcode on a Mac
```

**My honest recommendation:** publish on Google Play first. It's cheap, it accepts the app essentially as-is, and Android is the overwhelming majority of phones in Pakistan. Add native features and tackle Apple later, once people are actually using it.

---

## Part 3 — Two things that could get your app rejected

### 1. The supplier names

You asked for FESCO, GEPCO, IESCO and the rest to be named — they all are, and users pick their supplier when adding a meter. That's fine and useful.

What is risky is **naming or branding the app after them**. "FESCO Bill App" or using the FESCO logo implies official affiliation. Both stores reject apps that impersonate a brand, and these are government-owned companies whose names are protected.

This is why I named it **Bijli Meter Tracker** — neutral, memorable, yours. The supplier names appear only as a list of options, and the Backup screen carries this line:

> An independent tracking tool. Not affiliated with, endorsed by, or connected to any distribution company, PEPCO or WAPDA. Readings are entered by you.

Keep that disclaimer. Don't use any company's logo or colours as your app icon. If you ever add bill fetching from an official portal, get written permission first.

### 2. Privacy policy

Both stores require one, even for an app that collects nothing. Yours is genuinely simple — write a page saying: readings are stored only on the user's device, nothing is transmitted to any server, no analytics, no accounts, no third-party sharing, and the user can delete everything by uninstalling. Host it on the same GitHub Pages site and link it in both store listings.

---

## Part 4 — What's in the app

**Login and logout** — password set on first open, hashed with SHA-256 (never stored as plain text). The ⏻ button in the top right logs out; there's also a Log out button under Backup → Security, and Change password.

**Today** — one card per meter showing the reading as a digit register like a real meter dial. Tap a card to enter today's reading; it previews the units before you save and warns if the figure looks too high. Arrows move to any past date.

**Meters** — add unlimited connections. Each has: name, **supplier chosen from all 14 options**, meter number, consumer ID, reference number, opening reading and date, multiplier (MF) and dial digits. Each supplier has its own colour so you recognise it instantly.

**Report** — day-by-day table with each meter's units, **day total** and **accumulated running total**; a month-closing table with opening reading, closing reading, units and percentage share per meter; days with readings, average, peak day, estimated cost; a bar chart. Switch between **bill cycle** (8th → 7th, matching your reading date) and **calendar month**. Close the month to lock it, or reopen it. Export to CSV.

**Backup** — save a `.json` backup file, restore from one, export every reading as CSV, set tariff / currency / cycle start day.

**Suppliers covered:** LESCO, GEPCO, FESCO, IESCO, MEPCO, PESCO, HESCO, SEPCO, QESCO, TESCO, HAZECO, K-Electric, AJ&K Electricity Department, plus "Other supplier".

---

## Part 5 — Moving your existing data across

The desktop app and this mobile app store data separately. For your four FESCO meters, open **Meters → Add a meter** and enter each one from the bill:

| Name | Supplier | Meter no | Consumer ID | Reference no | Opening |
|---|---|---|---|---|---|
| Amir Hussain | FESCO | S-P 9887679 | 1132193218 | 06133440531600 | 3064 |
| Asif Raza Mir Shah | FESCO | S-P 299976 | 1135781907 | 06133440531603 | 775 |
| Asim Raza Mir Shah | FESCO | S-P 7643947 | 1132193219 | 06133440531601 | 331 |
| Kashif Raza Meer Shah | FESCO | S-P 1418469 | 1132193220 | 06133440531602 | 4607 |

Set the opening date to **2026-06-08**, or better, use the reading from your most recent bill so the first day doesn't show a month's worth of units.

---

## Part 6 — Changing the app later

It's one file: `index.html`. Open a new chat with Claude, attach it, and describe what you want — "add a photo of the meter to each reading", "add Urdu language", "let me record the actual bill amount and compare it against my tracked units", "add a reminder notification at 8pm". Replace the file, re-upload to GitHub Pages, and installed copies update automatically the next time they're opened online.
