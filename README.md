# Sang'a Fees Register (multi-device sync)

A single-file PWA for tracking parent fees, per-learner exam fees, and food
contributions (in KG) for Sang'a Comprehensive School — now with real-time
sync across devices via Firebase Firestore.

## 1. Create the Firebase project

1. Go to https://console.firebase.google.com → **Add project** → name it
   something like `sanga-fees` → finish the wizard (Analytics optional).
2. In the project, go to **Build → Firestore Database → Create database**.
   Choose **Production mode** and a region close to Kenya (e.g. `eur3` or
   `europe-west`).
3. Go to **Project settings** (gear icon) → **General** → scroll to
   **Your apps** → click the `</>` (web) icon → register an app (nickname
   e.g. "fees-web") → **do not** check Firebase Hosting.
4. Copy the `firebaseConfig` object shown (apiKey, authDomain, projectId,
   storageBucket, messagingSenderId, appId).
5. Open `index.html` in this repo, find the `FIREBASE_CONFIG` block near
   the top of the `<script>` section, and paste your real values in place
   of the `YOUR_...` placeholders.

## 2. Set Firestore security rules

By default Firestore blocks all access. Since this app only has a 4-digit
PIN (not real user accounts), the simplest workable rule set is to allow
read/write to anyone who has the app link — acceptable for an internal
school tool, but be aware it's not bank-grade security. In Firestore →
**Rules**, use:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if true;
    }
  }
}
```

(If you'd like tighter security later — e.g. requiring a shared secret key
or Firebase Auth — that's a further step we can add.)

## 3. Push this repo to GitHub

From this folder:

```bash
git init
git add .
git commit -m "Sang'a Fees Register with Firestore sync"
git branch -M main
git remote add origin https://github.com/<your-username>/<new-repo-name>.git
git push -u origin main
```

(Create the empty repo on GitHub first at https://github.com/new — no
README/gitignore needed, this folder already has one.)

## 4. Enable GitHub Pages

In the repo on GitHub: **Settings → Pages → Source: Deploy from a branch →
Branch: main / (root)** → Save. Your app will be live at:

```
https://<your-username>.github.io/<new-repo-name>/
```

## Notes

- Default login PIN is **2026** — change it from the ☰ menu once you're in;
  the new PIN syncs to Firestore so it works the same on every device.
- If a device is offline, the app keeps working from its local cache and
  syncs automatically once back online (the header shows 🟢 Synced /
  🟠 Offline / ⚪ Local device only).
- The first device to open the app (once Firebase is configured) seeds
  Firestore with the existing 61 families. After that, all devices read
  from the same Firestore data.
