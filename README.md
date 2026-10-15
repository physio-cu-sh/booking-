# PTCU Topic Booking — Firebase + GitHub Pages

## What's included
- `index.html`: responsive Arabic booking site with the 14 assignment topics.
- `firestore.rules`: starter Firestore rules.
- Each topic has 5 teams, 5 seats per team.
- Live booking counts are shared between visitors through Firestore.
- A student's normalized university ID is SHA-256 hashed in the browser and used as the booking document ID, to prevent duplicate bookings in the normal flow.

## Step 1 — Create Firebase project
1. Open https://console.firebase.google.com/ and sign in with your Google account.
2. Click **Create a project** and give it a name such as `ptcu-topic-booking`.
3. In the project, open **Build → Firestore Database** and create a database. Choose a region close to your users (for example, Europe if available); the database region cannot easily be changed later.
4. Open Project settings (gear icon) → **General** → **Your apps** → Web app (`</>`). Register the app and copy the `firebaseConfig` object.
5. In `index.html`, replace the six `PASTE_...` values in `firebaseConfig` with the real values. Keep the property names unchanged. Firebase web config is not a server secret, but database security rules are essential.

## Step 2 — Publish Firestore rules
1. In Firebase Console, go to **Firestore Database → Rules**.
2. Replace the editor contents with `firestore.rules` and click **Publish**.
3. These are starter rules, not high-assurance anti-fraud rules. Anyone can read names in the booking records, so use only if the class agrees to display booked names? Currently the page only displays counts, but Firestore reads are public. For a privacy-preserving production setup, use a Cloud Function / trusted backend and don't allow public reads of student records. The current client transaction also reads all bookings to check capacity, so the simple version trades privacy for ease of deployment.
4. Do not put any service-account/private key in the website.

## Step 3 — Deploy with GitHub Pages
1. Create a new repository on https://github.com/new, e.g. `ptcu-topic-booking`. Public repository is fine for a public static site; don't upload private keys.
2. Upload `index.html` to the repository root. You may also upload `firestore.rules` and this README for reference.
3. In repository **Settings → Pages**, select **Deploy from a branch**, choose `main` and `/ (root)`, then Save.
4. Wait for GitHub Pages to publish the site. It will show a URL similar to `https://YOUR-USERNAME.github.io/ptcu-topic-booking/`.

## Important production limitation
The starter version is useful for a small class pilot but it is not hardened against malicious users: public clients can potentially bypass the user interface or race capacity checks. Before relying on it for official allocation, the safer design is Firebase Authentication (student login) plus a callable Cloud Function that uses a Firestore transaction and server-side validation, with public access limited to aggregate counts. Never use this starter with sensitive data. It stores student names and hashes university numbers; hashes are not guaranteed anonymous if ID numbers are predictable.

## Testing checklist
- Open the public link in two different browsers/devices.
- Book a seat in one browser and confirm counts update in the other.
- Try to book with the same university ID again.
- Fill a team to 5, then confirm it disappears from available team choices.
- Fill a topic to 25, then confirm the topic is closed.
