# 🏸 Badminton Tournament Manager

A free, mobile-friendly web app to run a badminton tournament. Create teams, assign them into groups, generate round-robin matches, log scores from any phone or laptop, and watch the standings update in real time across all connected devices.

- **Single HTML file** — `index.html`. No build step.
- **Free hosting** on GitHub Pages (or Netlify, or any static host).
- **Multi-device live sync** via Firebase Realtime Database (free tier).
- **Standings logic**: Wins → Point difference → Points scored.

---

## What you'll set up (about 10 minutes total)

1. A free **Firebase** project — gives you the live-sync database.
2. A free **GitHub Pages** site — gives you a public URL.

Both are zero-cost. You only do this once.

---

## Part 1 — Create the Firebase project (≈5 min)

1. Open <https://console.firebase.google.com> and sign in with any Google account.
2. Click **Add project**.
   - Pick any name, e.g. `my-badminton`.
   - You can disable Google Analytics — not needed.
   - Click **Create project**, then **Continue** when ready.
3. In the left menu, expand **Build** → click **Realtime Database** → **Create Database**.
   - Pick any region (e.g. *us-central1* or one near you).
   - Choose **Start in test mode** when asked about rules. (We'll show you tighter rules below.)
   - Click **Enable**.
4. Click the gear ⚙ icon (top left) → **Project settings** → scroll to **Your apps** → click the `</>` (web) icon.
   - Give the app a nickname (anything) and click **Register app**.
   - Firebase shows a code block containing a `firebaseConfig` object. **Copy the whole `{ ... }` block** — you'll paste it into the app on first run.
5. Back in **Realtime Database → Rules**, replace the default rules with:

   ```json
   {
     "rules": {
       "tournaments": {
         ".read": true,
         "$tid": {
           ".write": true,
           ".indexOn": ["createdAt"]
         }
       }
     }
   }
   ```

   Click **Publish**. This lets anyone with the page URL view and update tournaments. Fine for a club event. (For a tighter setup with logins, switch to authenticated rules later.)

---

## Part 2 — Host the app on GitHub Pages (≈5 min)

1. Sign in at <https://github.com> (sign up is free).
2. Click **+** (top right) → **New repository**.
   - Name: `badminton` (or anything).
   - Set it to **Public**.
   - Tick **Add a README file**.
   - Click **Create repository**.
3. In the new repo, click **Add file → Upload files**. Drag `index.html` into the page. Click **Commit changes**.
4. Go to **Settings → Pages** (left sidebar).
   - Under **Build and deployment**, set **Source: Deploy from a branch**.
   - Branch: `main` / folder: `/ (root)`. Click **Save**.
5. Wait ~1 minute. The page will show a URL like:
   `https://YOUR-USERNAME.github.io/badminton/`
6. Open that URL on any device. You'll be prompted to paste your Firebase config (from Part 1, step 4). Paste and click **Save & continue**. Done.

> **Updating the app later**: if you want to change anything in `index.html`, just upload the new version to the repo. GitHub Pages republishes automatically in a minute or two.

### Alternative: Netlify drop (no account needed initially)

If you'd rather skip GitHub: open <https://app.netlify.com/drop>, drag the folder containing `index.html` into it. You'll get a public URL instantly. Same Firebase config setup applies.

---

## Using the app

### Day-of workflow

1. Open the public URL on the scoring laptop or phone.
2. **Tournaments → + New tournament**. Give it a name. The app generates a code like `BAD-9X3K`.
3. **Teams** tab → click **+ Add team** for each pair, or **Bulk add** to paste a list like:
   ```
   Vimal, Srini
   Raidu, Saravanen
   Madhu, Manoj
   …
   ```
4. **Groups** tab → **+ Add group**, set the name (e.g. *Group 1*) and **points to win** (e.g. 15 or 21). Add teams to the group from the dropdown. Click **Generate matches** to create the round-robin schedule.
5. **Matches** tab → enter scores as games finish. (Optional: add court & start time per match.)
6. **Standings** tab → live ranking, sorted by Wins → PD → PF.
7. **Knockout** tab → once group play is done (or even before, if you want a preview):
   - Set how many teams **qualify per group** (typically 1 or 2).
   - Set how many **wildcards** to add (next-best teams across all groups by W → PD → PF).
   - Total qualifiers must be **4** (Semi-Final + Final), **8** (QF + SF + Final), or **16** (R16 + QF + SF + Final).
   - Choose **points to win** for knockout matches (often 21).
   - Tick **3rd-place playoff** if you want one.
   - Click **Generate bracket** — the app seeds 1v8, 4v5, 3v6, 2v7 (or 1v4, 2v3 for 4-team) so the top seeds meet last. As you enter each match score, the winner automatically advances to the next round across all devices.
   - You can **re-generate** any time (e.g. after the last group game finishes) — this re-seeds from the latest standings and clears in-progress KO scores.

### Sharing with co-referees / spectators

Each tournament has a code (like `BAD-9X3K`). Two ways to share:

- **Just the code** — others type it on the home screen and click **Open**.
- **Direct link** — copy the share link from the tournament header. Anyone opening it is dropped straight into that tournament.

All connected devices see updates within ~1 second.

### Tournament data

- Lives in your own Firebase project under `/tournaments/<code>`.
- Survives across sessions — no expiry.
- Delete a tournament from its header when the event is over (or keep it as a record).

---

## Mobile use

Open the public URL in any mobile browser. Add it to your home screen for a quick-access icon (Safari: Share → *Add to Home Screen*; Chrome: ⋮ → *Add to Home screen*). Score-entry inputs use the numeric keypad on phones.

---

## Troubleshooting

- **"Config must include apiKey and databaseURL"** — make sure you completed Part 1 step 3 (creating the Realtime Database) **before** copying the config in step 4. The `databaseURL` field only appears after the database exists.
- **"Permission denied" or empty list** — check the rules in Firebase Console → Realtime Database → Rules. Use the rules from Part 1 step 5.
- **Two devices not syncing** — both must use the same Firebase config and the same tournament code. Try a hard refresh (Ctrl/Cmd+Shift+R).
- **I want to reset the device's saved config** — header → **⚙ Settings** → *Disconnect (clear local config)*.

---

## File layout

```
EBUC_scores/
├── index.html      # the entire app (HTML + CSS + JS)
└── README.md       # this file
```

That's it. No `npm install`, no build step.
