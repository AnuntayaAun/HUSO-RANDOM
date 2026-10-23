# HUSO RANDOM

วงล้อสุ่มสำหรับคณะมนุษยศาสตร์และสังคมศาสตร์ มหาวิทยาลัยบูรพา

## Local development

1. Install Node.js and dependencies with `npm install`.
2. Copy `.env.example` to `.env.local`.
3. Add a Firebase App Check reCAPTCHA v3 site key as `VITE_FIREBASE_APPCHECK_SITE_KEY`.
4. Run `npm run dev` and open `http://localhost:3000`.

The Firebase web configuration identifies the project and is public. Restrict its API key to the app's domains and required Firebase APIs in Google Cloud Console. Never put service-account credentials in this client application.

## Firebase setup

This app is configured for Firebase project `huso-random`.

1. In Firebase Console, enable Google under Authentication > Sign-in method. Add the deployed Hosting domain and `localhost` under Authorized domains.
2. Create a Cloud Firestore database.
3. In App Check, register the web app with reCAPTCHA v3, copy its site key to `.env.local`, then deploy/rebuild the app.
4. Deploy `firestore.rules` before enabling Firestore App Check enforcement. After verifying signed-in reads and writes, enable enforcement for Firestore (and Authentication if available for the project).
5. Add `huso-random.web.app` and any custom domain to the App Check/reCAPTCHA allowed domains.

Authentication accepts any Google account with a verified email. Firestore Rules independently enforce the Google provider, verified email, and matching UID. Guest wheel edits remain in memory and guest spins are not written to Firestore.

Signed-in workspace settings are stored at `users/{uid}/workspace/current`; saved presets are stored at `users/{uid}/presets/{presetId}`; spin history is stored at `users/{uid}/history/{historyId}`. The profile document at `users/{uid}` contains the display name, email, and photo URL.

## Deploy to Firebase Hosting

Authenticate the Firebase CLI once with `npx firebase-tools login`, then run:

```powershell
npm run deploy:firebase
```

The script builds the app and deploys Hosting plus Firestore Rules to the project in `.firebaserc`. Set the App Check site key in the environment used for the production build. Authentication provider settings and App Check enforcement are configured in Firebase Console, not by the deploy command.

## Data rules

`firestore.rules` denies unauthenticated access and prevents one account from reading or changing another account's workspace, presets, history, or profile. Profile deletion and history updates are denied; users can create and delete only their own history records.
