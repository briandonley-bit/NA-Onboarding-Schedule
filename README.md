# Sales Onboarding Scheduler

A single-page drag-and-drop tool for planning the 3-week sales onboarding schedule, built from `NA BD - June 26 Onboarding Schedule.csv`. No build step, no server — it's one `index.html` file plus a JSON data file, hosted for free on GitHub Pages.

## What it does

- Calendar view of Week 1 / Week 2 / Week 3, Monday–Friday, in 30-minute slots.
- Every time slot shows PST, CST, and EST simultaneously.
- **Not Yet Scheduled** sidebar lists meetings that haven't been placed on the calendar yet.
- Drag a meeting from the sidebar onto a day/time to schedule it. Drag a scheduled block to move it. Drag the bottom edge of a block to resize its duration.
- Click any block (scheduled or in the sidebar) to edit its title, subtitle, the person running it, duration, and color.
- "Add meeting" button creates a new unscheduled item.
- All changes save to a JSON file in a GitHub repo via the GitHub API, so every sales leader sees the same up-to-date schedule and every save is a real git commit (full history, diffs, and rollback for free on GitHub).

## One-time setup (5–10 minutes)

1. **Create a GitHub repo.** Any name, can be private. Example: `sales-onboarding-scheduler`.
2. **Upload these files** to the repo, keeping the folder structure:
   - `index.html`
   - `data/schedule.json`
3. **Enable GitHub Pages**: repo → Settings → Pages → Source: `Deploy from a branch` → Branch: `main` / root → Save. GitHub gives you a URL like `https://<org>.github.io/sales-onboarding-scheduler/`. That URL is the app — bookmark it and share it with the other sales leaders.
4. **Each sales leader creates their own Personal Access Token** (this is what lets the app save on your behalf; nobody shares a token):
   - GitHub → Settings → Developer settings → Fine-grained tokens → Generate new token.
   - Resource owner: your org. Repository access: only this repo.
   - Permissions → Contents: **Read and write**.
   - Copy the generated token (starts with `github_pat_...`).
5. **Open the app URL**, click **⚙ Settings**, and fill in:
   - Repo owner/org, repo name, branch (`main`), file path (`data/schedule.json`), and your personal token.
   - Click **Save & connect**. The schedule should load from GitHub.

Repeat step 4–5 once per person. After that, everyone just opens the same bookmarked URL.

## Day to day use

- **Save to GitHub** commits your current changes. The status pill in the header shows *Unsaved changes* vs *Synced with GitHub*.
- **Reload** pulls the latest version from GitHub (useful if someone else just saved).
- The app checks every 20 seconds for newer versions; if it finds one while you have unsaved edits, it shows a banner rather than overwriting your work — click it to reload once you're ready.
- If two people save at nearly the same moment, the second save is rejected with a conflict message and the app reloads the latest version automatically, so you never silently lose someone else's edit — just redo your change and save again.
- Full edit history (who changed what, when) lives in the repo's commit history on GitHub.

## Notes on the seed data

`data/schedule.json` was generated from your uploaded sheet (Weeks 1–3). A few things worth knowing:
- Some cells in the original sheet were ambiguous ("-", "CPU BLOCK", day names used as titles like "Tuesday / Sales Standup"). These came through as-is — just click and retitle them.
- The "Week 0" sections from the sheet (the generic pre-week draft and the "Isaiah/Tri" variant) were left out since the ask was a 3-week schedule; add anything from those as new backlog items if you want them tracked.
- `Owner` is blank for everything — add names as you assign them.

## Files

- `index.html` — the entire application (HTML/CSS/JS, no build step).
- `data/schedule.json` — the schedule data. This is the file the app reads/writes on GitHub.
- `source.csv` — your original uploaded sheet, kept for reference.
- `parse.py` — the one-time script used to convert `source.csv` into `data/schedule.json` (not needed to run the app).

## Limitations to be aware of

- This is not real-time like Google Docs — saves are git commits, so there's a short round trip (seconds) between one person saving and another seeing it via Reload or the 20-second auto-check.
- Each collaborator needs their own GitHub token with write access to the repo. Tokens are stored only in that person's browser (localStorage) and are never written into the schedule file or committed.
