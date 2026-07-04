# Sales Onboarding Scheduler

A single-page drag-and-drop tool for planning the 3-week sales onboarding schedule, built from `NA BD - June 26 Onboarding Schedule.csv`. No build step, no server — it's one `index.html` file plus a JSON data file, hosted for free on GitHub Pages.

## What it does

- Calendar view of Week 1 / Week 2 / Week 3, Monday–Friday, in 30-minute slots.
- Every time slot shows PST, CST, and EST simultaneously.
- **Not Yet Scheduled** sidebar lists meetings that haven't been placed on the calendar yet.
- Drag a meeting from the sidebar onto a day/time to schedule it. Drag a scheduled block to move it. Drag the bottom edge of a block to resize its duration.
- Click any block (scheduled or in the sidebar) to edit its title, subtitle, the person running it, duration, and color.
- "Add meeting" button creates a new unscheduled item.
- Each meeting has a **Notes** field for agenda/context — a 📝 icon shows on any block or list item that has notes, with the text as a hover tooltip.
- All changes save to a JSON file in a GitHub repo via the GitHub API, so every sales leader sees the same up-to-date schedule and every save is a real git commit (full history, diffs, and rollback for free on GitHub).
- **Calendar & Invites**: save named invite lists (e.g. "June Cohort") with a set of email addresses, assign a list to any meeting, and send/update real Google Calendar invites (with attendees) for a whole week at once. See the Google Calendar setup section below.

## One-time setup (5–10 minutes)

1. **Create a GitHub repo.** Any name, can be private. Example: `sales-onboarding-scheduler`.
2. **Upload these files** to the root of the repo (no subfolders needed):
   - `index.html`
   - `schedule.json`
   - `.nojekyll` (an empty file — see note below)
3. **Enable GitHub Pages**: repo → Settings → Pages → Source: `Deploy from a branch` → Branch: `main` / root → Save. GitHub gives you a URL like `https://<org>.github.io/sales-onboarding-scheduler/`. That URL is the app — bookmark it and share it with the other sales leaders.
4. **Each sales leader creates their own Personal Access Token** (this is what lets the app save on your behalf; nobody shares a token):
   - GitHub → Settings → Developer settings → Fine-grained tokens → Generate new token.
   - Resource owner: your org. Repository access: only this repo.
   - Permissions → Contents: **Read and write**.
   - Copy the generated token (starts with `github_pat_...`).
5. **Open the app URL**, click **⚙ Settings**, and fill in:
   - Repo owner/org, repo name, branch (`main`), file path (`schedule.json`), and your personal token.
   - Click **Save & connect**. The schedule should load from GitHub.

Repeat step 4–5 once per person. After that, everyone just opens the same bookmarked URL.

### Why the `.nojekyll` file

GitHub Pages runs everything through Jekyll by default, and Jekyll can fail on plain HTML/JS files that weren't written for it — that's the red "failure" mark you may see on a commit. Adding an empty file literally named `.nojekyll` to the repo root tells GitHub Pages to skip that step and just serve the files as-is. Add it once and this class of failure won't come back.

## Day to day use

- **Save to GitHub** commits your current changes. The status pill in the header shows *Unsaved changes* vs *Synced with GitHub*.
- **Reload** pulls the latest version from GitHub (useful if someone else just saved).
- The app checks every 20 seconds for newer versions; if it finds one while you have unsaved edits, it shows a banner rather than overwriting your work — click it to reload once you're ready.
- If two people save at nearly the same moment, the second save is rejected with a conflict message and the app reloads the latest version automatically, so you never silently lose someone else's edit — just redo your change and save again.
- Full edit history (who changed what, when) lives in the repo's commit history on GitHub.

## Setting up Google Calendar invites (optional)

This lets the app create real Google Calendar events — with your chosen invitees attached — for any meeting you assign an invite list to. It uses Google's sign-in directly in the browser; there's no separate backend.

**One-time setup (one person does this, ~10 minutes):**

1. Go to [console.cloud.google.com](https://console.cloud.google.com) and create a new project (any name, e.g. "Onboarding Scheduler").
2. In the search bar, find and enable the **Google Calendar API** for that project.
3. Go to **APIs & Services → OAuth consent screen**. If your Google account is on a Workspace domain (e.g. `@fresha.com`), choose **User type: Internal** — this skips Google's verification process entirely and restricts sign-in to people at your company, which is exactly what we want here. Fill in the required app name/support email and save.
4. Go to **APIs & Services → Credentials → Create Credentials → OAuth client ID**. Choose **Application type: Web application**.
5. Under **Authorized JavaScript origins**, add the exact URL(s) the app will be opened from — for example your GitHub Pages URL (`https://yourname.github.io`), and/or `http://localhost:8000` if you want to test it by running a local server. (Opening the file directly by double-clicking, i.e. a `file://` address, will **not** work for Google sign-in — it needs a real `http://` or `https://` origin.)
6. Click Create. Copy the **Client ID** shown (looks like `123456789-abc...apps.googleusercontent.com`) — this is not secret and can be shared with your team.
7. In the app, click **🗓️ Calendar & Invites**, paste the Client ID into the "Google account" field, and click **Connect Google Calendar**. Each person on the team does this connect step once with the same Client ID; sign-in itself is per-person.

**Using it day to day:**

- Click **🗓️ Calendar & Invites** to create/edit invite lists — give each one a name and a list of emails (one per line or comma-separated).
- Open any meeting and choose an invite list from the "Invite list" dropdown.
- Before sending invites for a week, set that week's **Monday's date** in the field above the calendar grid — this is what turns the abstract "Week 1, Monday, slot 3" into a real calendar date. Invite times are always sent in Eastern time.
- Click **📤 Send invites for this week** to create/update Google Calendar events (with attendees) for every meeting in the current week tab that has an invite list assigned. Running it again for the same week updates the existing events instead of creating duplicates.
- A "View in Google Calendar" link appears in a meeting's edit panel once it's been synced.

## Notes on the seed data

`schedule.json` was generated from your uploaded sheet (Weeks 1–3). A few things worth knowing:
- Some cells in the original sheet were ambiguous ("-", "CPU BLOCK", day names used as titles like "Tuesday / Sales Standup"). These came through as-is — just click and retitle them.
- The "Week 0" sections from the sheet (the generic pre-week draft and the "Isaiah/Tri" variant) were left out since the ask was a 3-week schedule; add anything from those as new backlog items if you want them tracked.
- `Owner` is blank for everything — add names as you assign them.

## Files

- `index.html` — the entire application (HTML/CSS/JS, no build step).
- `schedule.json` — the schedule data. This is the file the app reads/writes on GitHub.
- `.nojekyll` — empty file that tells GitHub Pages not to run Jekyll on this repo.
- `source.csv` — your original uploaded sheet, kept for reference.
- `parse.py` — the one-time script used to convert `source.csv` into `schedule.json` (not needed to run the app).

## Limitations to be aware of

- This is not real-time like Google Docs — saves are git commits, so there's a short round trip (seconds) between one person saving and another seeing it via Reload or the 20-second auto-check.
- Each collaborator needs their own GitHub token with write access to the repo. Tokens are stored only in that person's browser (localStorage) and are never written into the schedule file or committed.
- **Google Calendar invites won't work if you open `index.html` by double-clicking it** (a `file://` address) — Google's sign-in requires a real `http://` or `https://` origin. Until GitHub Pages is working, you can test this locally by running a tiny local server instead of double-clicking the file: open a terminal in this folder and run `python3 -m http.server 8000`, then visit `http://localhost:8000` in your browser (and add that exact address as an Authorized JavaScript origin in step 5 above). Drag-and-drop, editing, and GitHub sync all work fine either way — only the Calendar connect step needs a real origin.
