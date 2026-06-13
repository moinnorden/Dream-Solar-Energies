# 🔥 Firebase Setup Guide — Dream Solar Energies
> **Status: Firebase config already integrated** — your real project credentials are baked into all 3 files.

---

## Your Firebase Project

| Field | Value |
|---|---|
| **Project ID** | `dream-solar` |
| **Auth Domain** | `dream-solar.firebaseapp.com` |
| **Storage Bucket** | `dream-solar.firebasestorage.app` |
| **App ID** | `1:199990013532:web:36fd0adf30758694b40099` |

All 3 files (`index.html`, `login.html`, `admin.html`) already have this config — **no manual substitution needed**.

---

## Step 1 — Enable Authentication (if not done)

1. Go to **https://console.firebase.google.com → dream-solar**
2. **Build → Authentication → Get started**
3. Enable:
   - ✅ **Email/Password**
   - ✅ **Google** (add your support email)

---

## Step 2 — Create Firestore Database (if not done)

1. **Build → Firestore Database → Create database**
2. Start in **test mode** (update rules below)
3. Region: `asia-south1` (Mumbai — closest to Malegaon)

---

## Step 3 — Apply Security Rules

Go to **Firestore → Rules** and paste:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {

    match /users/{userId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
      allow read: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }

    match /queries/{queryId} {
      allow create: if true;
      allow read, update, delete: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }

    match /contactRequests/{id} {
      allow create: if true;
      allow read, delete: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }

    match /installations/{id} {
      allow read, write: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }

    match /testimonials/{id} {
      allow read: if true;
      allow write: if request.auth != null &&
        get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
    }
  }
}
```

---

## Step 4 — Set Up Admin Account

1. Open `login.html` → Create an account with your email
2. Go to **Firebase Console → Firestore → `users` collection**
3. Find your user document → Edit
4. Add/set field: `role` = `admin` (string type)
5. Now you have full access to `admin.html`

---

## Step 5 — Deploy to Firebase Hosting (Free)

```bash
npm install -g firebase-tools
firebase login
cd your-project-folder
firebase init hosting
# Choose: use existing project → dream-solar
# Public dir: . (current folder)
# Single page app: No
firebase deploy
```

Your live URL: `https://dream-solar.web.app`

---

## Firestore Collections

| Collection | Populated By | Managed In |
|---|---|---|
| `users` | Login / Register | Admin → Users |
| `queries` | Website query form | Admin → Queries |
| `contactRequests` | Contact form | Admin → Queries |
| `installations` | Admin panel | Admin → Installations |
| `testimonials` | Admin panel | Admin → Testimonials |

---

## File Summary

| File | Purpose |
|---|---|
| `index.html` | Main public website (Firebase query + contact forms) |
| `login.html` | User login & registration (Firebase Auth) |
| `admin.html` | Admin dashboard (Firestore read/write) |
| `SETUP.md` | This guide |

---

📞 **Support:** Er. Moin Hamdani · +91 87961 68099 · dreamsolar@gmail.com
