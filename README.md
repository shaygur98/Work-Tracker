[README.md](https://github.com/user-attachments/files/34133773/README.md)
# Work Time

A shift and salary tracker that runs entirely in the browser and can be installed as an app (PWA). Shifts are stored on the device (IndexedDB) and can optionally be backed up to your own Google Drive.

## Google Drive backup setup (one time, by whoever hosts the app)

The Client ID identifies the app, not a person. You create it once and commit it to `index.html`. Every user then signs in with their own Google account, and each user's backup goes to their own Drive. The Client ID is meant to be public: Google only accepts it from the origins listed below. If you fork this repo and host it at a different address, create your own Client ID or ask the owner to add your origin.

1. Go to <https://console.cloud.google.com/> and create a project (e.g. "Work Time").
2. **APIs & Services → Library**: search for **Google Drive API** and click **Enable**.
3. **APIs & Services → OAuth consent screen** (called **Google Auth Platform** in newer consoles):
   - User type: **External**. App name: "Work Time". Enter your email for support and developer contact.
   - Scopes: add `https://www.googleapis.com/auth/drive.appdata` ("See, create, and delete its own configuration data in your Google Drive").
   - **Audience → Publish app** (publishing status "In production"), so any Google account can sign in. While the app is in "Testing", only accounts listed as test users can sign in. `drive.appdata` is a non-sensitive scope, so publishing doesn't need Google's scope verification. Don't upload a logo unless you're ready for brand review: a logo triggers Google's brand verification.
4. **APIs & Services → Credentials → Create credentials → OAuth client ID**:
   - Application type: **Web application**.
   - **Authorized JavaScript origins**: the address the app is served from, with no path, e.g. `https://shaygur98.github.io`. For local testing, also add `http://localhost:8765`.
   - Leave **Authorized redirect URIs** empty.
5. Copy the Client ID (ends in `.apps.googleusercontent.com`) and paste it into `index.html`:

   ```js
   const GOOGLE_CLIENT_ID = 'xxxxxxxx.apps.googleusercontent.com';
   ```

Commit it. No client secret is involved, because the app runs entirely in the browser.

## How the backup works

- Tap **Connect Google Drive** and sign in. If a backup already exists (for example from another phone), its shifts are added to this device first, so connecting never overwrites your cloud data.
- After that, every saved, deleted or imported shift is uploaded automatically. If an upload fails (for example while you're offline), the card says so. The upload is retried when you're back online, or you can tap **Back up now**.
- **Restore** adds shifts from the cloud that are missing on this device. It never deletes anything on the device.
- The backup is one file in Drive's hidden app folder. The app can't see any of your other Drive files. You can see it, or delete it, under Drive → Settings → Manage apps.
- Google sign-ins last about an hour. After that, the next save or backup may briefly show a Google popup to sign in again.
