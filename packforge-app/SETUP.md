# Pack Forge — Setup Guide
Complete this once. Takes about 15 minutes.

---

## STEP 1 — Create a Firebase Project (free)

1. Go to **console.firebase.google.com**
2. Click **"Add project"**
3. Name it `packforge` → click Continue
4. Disable Google Analytics (you don't need it) → click **Create project**
5. Wait ~30 seconds for it to provision

---

## STEP 2 — Create a Firestore Database

1. In your Firebase project, click **"Firestore Database"** in the left sidebar
2. Click **"Create database"**
3. Choose **"Start in test mode"** (we'll lock it down properly in Step 5)
4. Select your closest region (e.g. `us-central1` for US) → click **Enable**

---

## STEP 3 — Enable Anonymous Authentication

This lets the app identify your device without requiring a login.

1. Click **"Authentication"** in the left sidebar
2. Click **"Get started"**
3. Under **Sign-in providers**, click **"Anonymous"**
4. Toggle it **on** → click **Save**

---

## STEP 4 — Get Your Firebase Config Keys

1. Click the **gear icon ⚙** next to "Project Overview" → **Project settings**
2. Scroll down to **"Your apps"** section
3. Click the **web icon `</>`**
4. Register app name: `packforge-web` → click **Register app**
5. You'll see a block like this — **copy all of it**:

```js
const firebaseConfig = {
  apiKey: "AIza...",
  authDomain: "packforge-xxxxx.firebaseapp.com",
  projectId: "packforge-xxxxx",
  storageBucket: "packforge-xxxxx.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc123"
};
```

6. Open **index.html** in a text editor
7. Find the line near the top that says:

```js
// ► PASTE YOUR FIREBASE CONFIG HERE
const firebaseConfig = { REPLACE_ME: true };
```

8. Replace that entire object with your copied config

---

## STEP 5 — Lock Down Firestore Security Rules

1. In Firebase Console → **Firestore Database** → **Rules** tab
2. Replace the rules with this:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /packforge/{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

3. Click **Publish**

This means only authenticated sessions (your anonymous auth) can read/write. No one else can touch your data.

---

## STEP 6 — Deploy to Vercel (free, gets you a real URL)

### Option A — Drag & Drop (easiest, no account needed initially)
1. Go to **vercel.com** → click **"Start Deploying"**
2. Choose **"Browse"** to upload files
3. Drag the entire `packforge-app` folder
4. Vercel gives you a URL like `packforge-app-xyz.vercel.app`
5. That's your app. Open it on any device.

### Option B — GitHub (recommended for easy updates)
1. Create a free account at **github.com**
2. Create a new repository called `packforge`
3. Upload the `packforge-app` folder contents
4. Go to **vercel.com** → **"Import Git Repository"**
5. Connect your GitHub → select `packforge` repo → click **Deploy**
6. Every time you update files and push to GitHub, Vercel auto-redeploys

---

## STEP 7 — Add to iPhone Home Screen (makes it feel like an app)

1. Open your Vercel URL in **Safari** on your iPhone
2. Tap the **Share button** (box with arrow)
3. Scroll down and tap **"Add to Home Screen"**
4. Name it `Pack Forge` → tap **Add**
5. It now launches full-screen like a native app with its own icon

---

## How syncing works

- Every quantity change, new product, filament update, etc. **writes to Firestore instantly**
- Opening the app on any device (phone, tablet, computer) **loads live data from Firestore**
- A small sync indicator in the header shows **Saving...** while a write is in flight and **✓ Saved** when complete
- If you go offline, changes queue locally and sync automatically when connection returns

---

## Firestore data structure (for reference)

```
packforge/
  inventory        — all component quantities, parts, variants, combos
  packs            — pack definitions, tiers, compositions
  soloItems        — standalone dragon/gothic listings
  otherComponents  — keyboard clickers, packaging, etc.
  filament         — spool inventory by manufacturer/color
  meta             — nextId counters
```

---

## Costs

**Free forever** at your usage level:
- Firestore free tier: 50,000 reads/day, 20,000 writes/day, 1 GB storage
- Vercel free tier: unlimited deployments, 100 GB bandwidth/month
- You would need to print and sell an extraordinary volume before hitting any limits
