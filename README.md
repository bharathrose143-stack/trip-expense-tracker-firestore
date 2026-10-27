# Trip Ledger

A mobile-friendly shared trip expense tracker for GitHub Pages and Firebase. Families and members are editable; by default, every expense is split across families in proportion to their member counts. For example, family sizes 4, 2, and 2 divide a ₹800 expense as ₹400, ₹200, and ₹200. An expense can also exclude selected families, use a per-person calculation, or use custom family amounts.

## How this differs from the earlier uploaded version

The earlier `index.html` and `Code.gs` use a Google Sheet through a Google Apps Script web app. The page has six fixed names and stores individually shared expenses; it does not connect to Firestore or Firebase Authentication. Firebase Hosting can host that page, but the data requests still go to Apps Script. This replacement package uses Firestore for shared data and Google sign-in, as requested. Keep the original files and spreadsheet unchanged while setting up and reviewing this replacement.

Existing spreadsheet expenses are not imported automatically. The new Firestore trip starts with an empty expense list; retain the old sheet as the historical record unless you later choose to migrate those rows.

## Files

- `index.html` — complete static web app.
- `firestore.rules` — authenticated access controls for trips, members, expenses, families, and invite codes.

## Firebase setup

1. In Firebase Console, open the existing project and register a Web app if one is not registered yet.
2. In **Authentication → Sign-in method**, enable **Google**.
3. In **Authentication → Settings → Authorized domains**, add the exact GitHub Pages host (for example, `yourname.github.io`).
4. Create or open **Firestore Database**.
5. Your existing web app is registered. Its project ID (`trip-expense-tracker-a45ed`) and app ID are already filled into `index.html`. Open **Project settings → General → Your apps → Trip Expense Tracker → SDK setup and configuration → Config**, then copy its `apiKey` into the configured value if needed. Confirm the other values match the registered app. The API key is a browser identifier; do not put a service account key or Admin SDK private key in this static site.
6. In **Firestore → Rules**, publish the contents of `firestore.rules`. Do not leave Firestore in test mode.
7. Save `index.html` and `firestore.rules` in the root of your GitHub repository.

The Firebase web config identifies your project; it is not a server credential. Firestore Rules enforce access. Never put a service account key or Admin SDK private key in this static site.

## GitHub Pages

1. Create or open your `trip-expense-tracker` GitHub repository.
2. Upload `index.html` to the repository root and commit it. Keep `firestore.rules` in the repository as your deployable rules source.
3. Open **Settings → Pages**. Choose **Deploy from a branch**, select `main` and `/(root)`, then save.
4. Wait for the Pages deployment and open the URL shown under **Settings → Pages**.
5. Sign in with Google. Create a trip, add families and named members, then share the invite code with the other travelers. Each traveler signs in and enters that code.

## Sharing behavior

- Default family split: proportional to the number of named members in each family.
- Per-person: currently uses the same member-count weighting, with a distinct saved mode so the intent is visible per expense.
- Exclusions: selected families pay none of that expense; the remainder is redistributed by headcount.
- Custom: enter each family’s amount; they must total the expense amount.
- Balances are family-level net contributions (paid minus owed).
- Firestore listeners update the trip, family list, and expenses live for signed-in members.

## Important access note

Invite codes are random, high-entropy values and should be shared privately. Rotating the code deletes the prior invite document, so the previous code is revoked. The rules allow signed-in users to read a known invite code so a member can join, and only trip members can read or change trip data.

## Notes

- This is a static client app: Google sign-in is the authentication provider; Firestore Rules protect all database reads and writes.
- The browser remembers the last trip on that device. Sign out does not delete trip data.
- The deployment loads Firebase SDK modules from `gstatic.com`, so visitors need internet access.
