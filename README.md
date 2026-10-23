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
   **The first account ever created becomes the admin**, so do this yourself before sharing the address.
2. Pick **Meet Director**. A new account starts with no meets. Create one or import a meet program;
   new meets are shared live automatically and belong to your account.
3. Use **Access** to give each official their QR code. Officials who scan one are signed in as a guest
   and land on their role; meet staff sign in with their own email account.

## Meets that use another clerking method
On **Setup → Meet**, the meet director can untick **Use clerking in this meet**. Check-in, the start line, call
announcements and the clerking roles are then hidden; umpires see every entered athlete as competing, and
Athlete Status follows each athlete from the race through review to the result. Shoe check stays available, with its
officials named on the director's Setup page. Ticking the box again brings clerking back with nothing lost.

## Combined events coordinators
On the meet director's **Setup** page, the **Combined events** card turns on one or two coordinators, each running the
women's, men's or both combined events. Every combined-event discipline in the meet program (track and field) then
goes to them instead of clerking: they check athletes in and scratch them, change start times (umpires see the change),
note when each event starts and finishes, and see a countdown to the next event once its start time is set.
Each coordinator has an access code on the **Access** page.

## Updating the app later
Replace `index.html` in the repository (**Add file → Upload files**, same name) and commit.
GitHub Pages republishes within a couple of minutes. If the rules change, paste the new
`firestore.rules` into Firebase → Firestore → Rules and publish.

## Accounts and the Admin screen
Every person who signs in with an email gets an account. On the **Meets** tab, the account card shows your
name (with **Edit name**), **Sign out**, and for admins an **Admin** button.

The Admin screen lists **all accounts** (search, Export CSV). For each account an admin can:
- **View meets** — the meets that account owns, active and archived. From there, **Open** a meet on this device,
  or **Archive** / **Restore** it. An archived meet is read-only for everyone until it's restored.
- **Suspend** / **Lift suspension** — a suspended account can still sign in and look, but can't change anything
  or start new meets.
- **Make admin** / **Remove admin**.
- See its **Access log**: suspensions, admin changes, meets archived or restored by an admin, and changes it tried
  to make that were refused.

Deleting accounts or meets isn't done from the app. To remove a login entirely, use Firebase → Authentication → Users.

## Who can do what (firestore.rules)
- **Meet staff** (email accounts): see and manage only the meets they own; open other meets through access codes.
- **Guest officials** (from an access code): work in the meet named in their code. They can't list, create or delete meets.
- **Admins**: see every account and meet, suspend accounts, archive and restore any meet, add or remove admins.
- **Suspended accounts** and **archived meets** can't be changed (admins can still restore them).
- **Shoe photos:** anyone signed in can view them; only the person who took a photo, or an admin, can remove it.
- Anyone who has the app's address can create an account, so share the address with your officials only.
  An admin can suspend any account that shouldn't be there.

## Limits on the free plan
Firestore's Spark plan allows 1 GiB of stored data, 50,000 reads and 20,000 writes a day. A shoe photo is about
0.3–0.7 MB, so roughly 1,500+ photos fit. A busy meet day with a dozen devices is well inside the daily limits.
Delete finished meets you no longer need to free space.

## Data is separate from the claude.ai version
This copy has its own database. Meets made in the claude.ai version don't appear here automatically.
To move one across, use **Meets → Export** there and **Meets → Import backup** here. Umpire calls and shoe
photos stay with the copy they were made in.
