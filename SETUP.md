# Qadriya Masjid — Live Admin Setup (Firebase, free)

This site now has two pages:
- `index.html` — the public website (loads content live from Firebase)
- `admin.html` — login-protected page for the committee to edit everything

Do this ONE-TIME setup (~15 minutes), then you never touch code again —
all future updates happen through the admin page in your browser.

---

## STEP 1 — Create a Firebase project
1. Go to https://console.firebase.google.com and sign in with any Google account.
2. Click "Add project" → name it e.g. `qadriya-masjid` → continue through
   the prompts (you can disable Google Analytics, it's not needed) → Create.

## STEP 2 — Register a Web App
1. On the project's home screen, click the `</>` (Web) icon to add a web app.
2. Nickname it "Qadriya Masjid Site" → Register app.
3. Firebase shows you a `firebaseConfig = { ... }` object. Copy the whole thing.
4. Open `firebase-config.js` in this folder and replace the placeholder
   object with the one you just copied. Save the file.

## STEP 3 — Turn on Firestore (the database)
1. In the left sidebar: Build → Firestore Database → Create database.
2. Choose "Start in production mode" → pick any nearby location → Enable.
3. Go to the "Rules" tab and replace the contents with:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /site/content {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```
Click Publish. (This means: anyone can view the site's content, but only
a logged-in committee member can change it.)

## STEP 4 — Turn on Authentication (the login system)
1. Left sidebar: Build → Authentication → Get started.
2. Under "Sign-in method", enable Email/Password → Save.
3. Go to the "Users" tab → Add user. Enter the committee's admin email
   and choose a strong password. This is what you'll log in with on
   `admin.html`. Add one entry per committee member who should have access.

## STEP 5 — Turn on Storage (for photos)
1. Left sidebar: Build → Storage → Get started → keep default settings → Done.
2. Go to the "Rules" tab and replace the contents with:

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read: if true;
      allow write: if request.auth != null;
    }
  }
}
```
Click Publish.

## STEP 6 — Upload the site
Upload all four files in this folder (`index.html`, `admin.html`,
`firebase-config.js`, and this `SETUP.md` is optional) to your GitHub
repo exactly as before, replacing the old `index.html`.

## STEP 7 — Try it
1. Visit `https://abdulquadiransari.github.io/QadriyaMasjid/admin.html`
2. Log in with the email/password you created in Step 4.
3. Fill in prayer times, committee members, events, etc. and hit Save
   on each tab.
4. Open the main site in another tab — your changes appear immediately,
   for everyone, with no re-upload needed.

---

### Notes
- Everything in Firestore/Storage is on Firebase's free "Spark" plan,
  which comfortably covers a small mosque site's traffic at no cost.
- Add more committee members as admin users any time via
  Authentication → Users → Add user — no code changes needed.
- If you ever forget a password, reset it from the same Users tab.
