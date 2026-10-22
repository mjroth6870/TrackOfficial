# Track Officials — hosted copy

One meet program for clerks, umpires and shoe check: check-in, start-line staging, umpire calls,
referee decisions, finals, shoe photos, and each athlete's status from check-in to the result.

This copy runs on **GitHub Pages** and keeps its data in **Firebase** (Firestore for the meet data and
shoe photos, Firebase Authentication for sign-in). It is the same app as the claude.ai version, with a
sign-in screen added.

## What's in this repository

| File | What it is |
|---|---|
| `index.html` | The whole app. GitHub Pages serves it. |
| `firestore.rules` | Who can read and change what. Paste into Firebase → Firestore → Rules. |
| `firebase.json` | Points the Firebase command-line tool at the rules file (only needed if you use the CLI). |
| `README.md` | This file. |

## One-time setup

### 1. Create the Firebase project
1. Go to <https://console.firebase.google.com> and choose **Create a project**.
2. Name it (for example `track-officials`). Google Analytics is not needed; turn it off.
3. On the project's home page, click the **Web** icon (`</>`) to add a web app. Give it a nickname
   (for example `Track Officials web`). Leave **Firebase Hosting** unticked. Click **Register app**.
4. Firebase shows a block of code containing `const firebaseConfig = { ... }`. Copy that block.
   It is safe to share: the web config is public by design, and the security rules are what protect the data.
   Put it into `index.html`: find the line `var CFG = null;` and replace `null` with the `{ ... }` part of that block.
   (Or hand the block to whoever builds the app and they'll return an `index.html` with it filled in.)
   Until this is done the app still opens, but keeps everything on the one device.

### 2. Turn on sign-in
In the Firebase console: **Build → Authentication → Get started → Sign-in method**.
1. Enable **Email/Password**. Inside it, also enable **Email link (passwordless sign-in)**. Save.
2. Add a provider: enable **Anonymous**. Save.
   (Anonymous is how officials who scan an access code get in without making an account.)

### 3. Create the database and its rules
1. **Build → Firestore Database → Create database.**
2. Choose a location near your meets (for example `nam5 (United States)`). This can't be changed later.
3. Start in **production mode**.
4. Open the **Rules** tab, replace everything there with the contents of `firestore.rules`, and click **Publish**.

The free **Spark** plan is enough. Shoe photos are stored inside Firestore itself, so no Cloud Storage
bucket or billing account is needed.

### 4. Create the GitHub repository and publish
1. On GitHub, create a new repository (for example `track-officials`). It must be **Public** for
   GitHub Pages to work on a free account.
2. **Add file → Upload files**, drag in `index.html`, `firestore.rules`, `firebase.json` and `README.md`,
   and **Commit changes**.
3. **Settings → Pages.** Under *Build and deployment*, set **Source** to *Deploy from a branch*,
   **Branch** to `main` and folder `/ (root)`. Save.
4. After a minute or two the page shows the address, for example
   `https://YOUR-GITHUB-NAME.github.io/track-officials/`.

### 5. Let the app sign people in from that address
In Firebase: **Authentication → Settings → Authorized domains → Add domain**, and add
`YOUR-GITHUB-NAME.github.io` (just the domain, no `https://` and no path).

### 6. First use
1. Open the GitHub Pages address and choose **Create account** with your email and a password.
2. Pick **Meet Director**, then create or import a meet. New meets are shared live automatically.
3. Use **Access** to give each official their QR code. Officials who scan one are signed in as a guest
   and land on their role; meet staff sign in with their own email account.

## Updating the app later
Replace `index.html` in the repository (**Add file → Upload files**, same name) and commit.
GitHub Pages republishes within a couple of minutes. If the rules change, paste the new
`firestore.rules` into Firebase → Firestore → Rules and publish.

## Who can do what (firestore.rules)
- **Meet staff** (signed in with an email account): see the list of meets, create and delete meets, and everything below.
- **Guest officials** (signed in from an access code): open and work in the meet named in their code. They can't list
  other meets or create or delete meets.
- **Shoe photos:** anyone signed in can view them; only the person who took a photo can remove it.
- Anyone who has the app's address can create an email account. Which screens a person sees depends on the role
  picked on their device, so treat the address as something to share with your officials only.

## Limits on the free plan
Firestore's Spark plan allows 1 GiB of stored data, 50,000 reads and 20,000 writes a day. A shoe photo is about
0.3–0.7 MB, so roughly 1,500+ photos fit. A busy meet day with a dozen devices is well inside the daily limits.
Delete finished meets you no longer need to free space.

## Data is separate from the claude.ai version
This copy has its own database. Meets made in the claude.ai version don't appear here automatically.
To move one across, use **Meets → Export** there and **Meets → Import backup** here. Umpire calls and shoe
photos stay with the copy they were made in.
