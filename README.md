# Hang — your wardrobe assistant

This folder contains everything you need to run your own private, free copy of Hang —
synced across your laptop and phone using your own Firebase project.

## What's in this folder

- `index.html` — the whole app (UI + logic)
- `config.js` — where you paste your Firebase project's keys (currently empty placeholders)
- `README.md` — this file

## Step 1 — Create a free Firebase project

1. Go to **https://console.firebase.google.com** and sign in with any Google account.
2. Click **Add project**, give it any name (e.g. "my-wardrobe"), and click through the
   setup screens. You can decline Google Analytics — you don't need it.
3. Once the project is created, you'll land on the project dashboard.

## Step 2 — Register a web app

1. On the project dashboard, click the **`</>`** (web) icon to add a web app.
2. Give it any nickname (e.g. "hang-web"). You don't need Firebase Hosting checked.
3. Click **Register app**. Firebase will show a code block that looks like this:

   ```js
   const firebaseConfig = {
     apiKey: "AIza...",
     authDomain: "my-wardrobe-1234.firebaseapp.com",
     projectId: "my-wardrobe-1234",
     storageBucket: "my-wardrobe-1234.appspot.com",
     messagingSenderId: "123456789",
     appId: "1:123456789:web:abcdef123456"
   };
   ```

4. Open `config.js` in this folder and replace each `PASTE_...` placeholder with the
   matching value shown above. Save the file.

## Step 3 — Turn on Firestore (the database)

1. In the left sidebar of the Firebase console, click **Build > Firestore Database**.
2. Click **Create database**.
3. Choose **Start in production mode** (we'll set proper rules in Step 5), pick any
   location close to you, and click **Enable**.

## Step 4 — Turn on Email/Password sign-in

This is what lets you and your friends each create your own private login.

1. In the left sidebar, click **Build > Authentication**.
2. Click **Get started** (if you haven't used Authentication before).
3. Under **Sign-in method**, click **Email/Password**, toggle it **Enable**, and click **Save**.

## Step 5 — Set Firestore security rules

1. Back in **Firestore Database**, click the **Rules** tab.
2. Replace the contents with:

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

3. Click **Publish**.

This means: each signed-in person can only ever read or write their *own* closet —
not anyone else's — even though everyone shares the same deployed app and the same
Firebase project.

## Step 6 — Try it locally

Open `index.html` in your browser (double-click it, or drag it into a browser tab).
If `config.js` is filled in correctly, you'll see a sign-in screen. Click **Create
one** to make your first account (any email + a password of 6+ characters — it
doesn't need to be a real, checkable email address, just one you'll remember).
Add an item to confirm it saves — refresh the page, sign back in, and it should
still be there.

If you see a "one quick setup step" screen instead of a sign-in form, double-check
`config.js` was saved with your real values (no leftover `PASTE_...` text).

**About "Take a photo":** browsers only allow camera access on HTTPS sites (or
`localhost`) for privacy reasons — not on a file opened directly from disk. So the
"Take a photo" button won't work while testing locally by double-clicking
`index.html`; "Choose from library" will always work, and once you deploy in
Step 7 the camera button will work too, since your deployed URL is HTTPS.

## Step 7 — Deploy it so it has its own URL (free)

**Easiest: Netlify Drop**
1. Go to **https://app.netlify.com/drop**
2. Drag the whole folder (all three files) onto the page.
3. Netlify gives you a live URL immediately — no account required for this method,
   though creating a free account lets you keep and update the same site later.

**Alternative: GitHub Pages**
1. Create a new repository on GitHub and upload `index.html`, `config.js`.
   (`README.md` is optional to include.)
2. In the repo, go to **Settings > Pages**, set the source to your main branch,
   and save.
3. GitHub gives you a URL like `https://yourname.github.io/reponame/`.

## Sharing it with friends

Once deployed, just send friends the URL. Each person clicks **Create one** on the
sign-in screen and picks their own email + password — everyone gets their own
private closet, kept completely separate by the security rule from Step 5. You
don't need to do anything per-friend in the Firebase console; accounts are created
by whoever signs up.

There's no invite gate — anyone who has the link can create an account. Since
you're not planning to publicize the URL widely, that's a reasonable trade-off for
a small group of friends. If you'd ever want a harder gate (e.g. an approved-emails
list), that's a small addition we can make later.

## Is this secure?

Reasonably, for a small group of friends. Your Firebase config values (in
`config.js`) are visible to anyone who views your deployed site's source code —
that's normal and expected for any app that runs entirely in the browser with no
separate server; Firebase is designed with this in mind. What actually protects
everyone's data is:

- **Real accounts** — each person signs in with their own email + password, not a
  shared or anonymous session.
- **The Firestore security rule** from Step 5 — it checks that the request's signed-in
  user ID matches the folder being read or written, so person A can never read or
  write person B's closet, even though they're both using the same app and project.

The main thing to keep in mind: there's no invite gate, so anyone with the URL can
create an account (see "Sharing it with friends" above). For a small, unpublicized
group that's a reasonable trade-off.

## Free tier limits (should be more than enough)

Firebase's free "Spark" plan gives you 1 GB of Firestore storage and 50,000
reads/writes per day, with no auto-pause — it stays live indefinitely even if you
don't open the app for a while. Your clothing photos are stored as compressed,
resized images directly inside your item records (not as separate files), which
keeps everything on this free tier without needing Firebase's paid Cloud Storage.

If you ever hit a "could not save" error on an item with a photo, it's most likely
one photo that's unusually large — try re-uploading it (background removal +
compression will usually shrink it comfortably below Firestore's 1 MB per-item
limit).

## Updating the app later

If you ask for new features or fixes down the line, you'll just get an updated
`index.html` to swap into this folder (or re-drag into Netlify) — `config.js` never
needs to change again once it's set up.
