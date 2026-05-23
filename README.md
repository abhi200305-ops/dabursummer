# Dabur Beat App — Single-file edition

A live, gamified, Firebase-backed dashboard for SUNSHINE TRADERS SSMs. Everything sits in **one `index.html`** — login, SSM scorecard, admin upload, monitor — like the Real ECO Dashboard pattern. SSMs log in with a PIN, mark visits, points + leaderboard update live for everyone.

## Files

```
dabur-beat-app/
├── index.html              ← The whole app (HTML + CSS + JS in one file)
├── assets/
│   └── firebase-config.js  ← Your Firebase project config (already filled for "dabur-summer")
├── seed/
│   ├── shops.json          ← 436 RTL shops with LM/MTD baselines (April + May)
│   ├── ssms.json           ← 3 SSMs with default PINs
│   └── config.json         ← Focus brands, point values, admin password
├── firestore.rules         ← Security rules (require Auth; tighten before production)
├── firebase.json           ← Hosting config
└── README.md
```

## Scoring

| Action | Points |
|---|---|
| 🔥 Activate a **DORMANT** outlet (not LM AND not MTD) | **+100** |
| ⚠ Recover a **SLIPPING** outlet (LM but not yet MTD) | **+50** |
| ✨ Each **NEW focus brand** added (was 0 in LM, billed now) | **+20** each |

"Served" = at least one focus brand billed. Eight focus brands: Activ 100%, Coconut APET, Coconut PET (priority); Hajmola Zeera, Real Mini, Real Koolerz Tetra, Real Koolerz Pet, Real Pet 250ml.

## Default credentials

| Role | Login |
|---|---|
| Admin | password `Abhinav_Dabur2025` |
| Anil Kumar Sharma | PIN `1001` |
| Harsh vardhan | PIN `1002` |
| Vikram Kumar singh | PIN `1003` |

Change all of these in Admin → Settings / SSM PINs.

---

## Setup (~10 minutes, one time)

### 1. Confirm your Firebase project exists

The bundled `assets/firebase-config.js` is already filled for the project **`dabur-summer`**. If that's yours, skip to step 2. Otherwise replace the config with your project's snippet (Firebase Console → ⚙️ Project settings → Your apps → Config).

### 2. Enable Anonymous Auth

Firebase Console → Build → **Authentication** → Get started → Sign-in method → **Anonymous** → Enable.

### 3. Create Firestore database

Build → **Firestore Database** → Create database → production mode → pick `asia-south1` (Mumbai) for low latency in Delhi. After it's created, open the **Rules** tab and paste the contents of `firestore.rules` from this folder → Publish.

### 4. Deploy

```bash
npm install -g firebase-tools
firebase login
cd dabur-beat-app
firebase use --add        # pick the dabur-summer project
firebase deploy
```

You'll get a URL like `https://dabur-summer.web.app`. Share it with the SSMs.

### 5. Seed initial data (once)

1. Open the URL → **🔒 Admin Login** → enter `Abhinav_Dabur2025`.
2. Go to the **📥 Upload** tab.
3. Scroll to **🌱 Initial / Seed Data** → click **Seed initial data from /seed/\*.json**.
4. Wait ~10 seconds. Toast says "Seed complete!". Reload.

Done. SSMs can now log in.

---

## Day-to-day

**SSM** — Opens URL on phone → picks name → enters PIN → sees outlets sorted Dormant → Slipping → Rising → On-track → taps **Mark Visit** on a shop → ticks the focus brands billed → Save. Points + tier badge update live; leaderboard refreshes for everyone.

**Admin** — Logs in with the password → **Monitor** for live KPIs, scorecard, beat progress, recent visits feed. **Upload** to push a fresh Retailer Brand Purchase report — drop the `.xls` or `.csv` → preview → commit (overwrites each shop's LM/MTD baseline). **SSM PINs** to rotate PINs or add SSMs. **Settings** to change scoring values or admin password. **Danger Zone** wipes visits + scores (keeps shop master, resets MTD to last-uploaded baseline).

---

## Security notes (read before wider rollout)

This is an internship prototype.
- PINs and admin password live in Firestore in plain text — anyone signed in (which is everyone, anonymously) can read them.
- All scoring runs in the browser; a clever SSM with DevTools could fake points.
- Firestore rules only check that the user is signed in, not which role they have.

To harden: switch SSMs to real Firebase Auth (phone OTP works well in India), move `recordVisitTx` into a Cloud Function, use Auth custom claims for the admin role, and tighten the Firestore rules to allow only the intended writer for each collection.

---

## Troubleshooting

- **"⚠ Not configured" pill** → `assets/firebase-config.js` still has placeholders.
- **Cloud pill stuck on "Connecting…"** → Anonymous Auth isn't enabled, or your network is blocking Firestore websockets.
- **SSM dropdown empty** → Seed hasn't been run, or Firestore rules block reads.
- **Admin login fails before seeding** → Use the default password `Abhinav_Dabur2025`; the app accepts it as a fallback to let you seed.

---

Built with Firebase v10 compat SDK · No build step · Edit `index.html` and refresh.
