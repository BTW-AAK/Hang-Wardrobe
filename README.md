# Hang

A private wardrobe assistant. Catalog your closet, build outfits, and get daily
outfit suggestions scored on color coordination, occasion, and laundry status -
synced across devices through your own Firebase project.

## Features

- Closet inventory with photo uploads, automatic background removal, and auto-detected colors
- Bulk photo upload - add several items at once, with a one-tap category pick per photo
- In-app camera capture with alignment guides for consistent photos
- Outfit builder with a live color-compatibility score
- Daily outfit suggestions, filtered by occasion and laundry status
- Laundry tracking with per-category wash-cycle defaults
- Multi-user accounts - each person signs in and gets their own private closet
- Responsive layout for desktop and mobile

## Stack

Single-file HTML/CSS/JS, no build step. Firebase (Firestore + Authentication) for
storage and accounts. Background removal runs client-side via
[`@imgly/background-removal`](https://github.com/imgly/background-removal-js).

## Setup

### 1. Create a Firebase project

Go to the [Firebase console](https://console.firebase.google.com) and create a
new project. Google Analytics is optional.

### 2. Register a web app

From the project dashboard, click **`</>`** to add a web app (Firebase Hosting not
required). Copy the resulting `firebaseConfig` object into `config.js`, replacing
the placeholder values.

### 3. Enable Firestore

**Build → Firestore Database → Create database.** Start in production mode.

### 4. Enable Email/Password authentication

**Build → Authentication → Sign-in method → Email/Password → Enable.**

### 5. Set security rules

In **Firestore Database → Rules**, replace the default with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

This scopes every account to its own data — no user can read or write another's.

### 6. Run it

Open `index.html` directly in a browser to test. File uploads work locally;
camera capture requires HTTPS, so it's only available once deployed.

### 7. Deploy

**Netlify Drop** — drag the project folder onto
[app.netlify.com/drop](https://app.netlify.com/drop) for an instant URL.

**GitHub Pages** — push `index.html` and `config.js` to a repo, then enable Pages
under repo settings.

## Multi-user access

Any visitor can create an account from the sign-in screen — there's no invite
gate. Each account's data is isolated by the security rule above. For a public
deployment, consider adding an email allowlist to the rules.

## Security notes

- `config.js` values are visible in your deployed site's source. This is expected
  for Firebase web apps and isn't a secret by itself.
- Actual access control comes from Firebase Authentication plus the per-user
  Firestore rule above.
- Firebase's free tier covers this comfortably: 1 GB Firestore storage, 50k
  reads/writes per day, no auto-pause. Photos are stored inline as compressed
  images rather than via Cloud Storage, keeping everything on the free tier.
- If a save fails on an item with a photo, it's usually one unusually large
  image — re-uploading it will re-run compression and should resolve it.
## License

MIT (or your license of choice).
