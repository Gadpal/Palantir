# Palantir

A personal work console, packaged as a real Windows desktop app. One place to see every project, work or personal, instead of a flat checklist that squashes all of it into the same shape.
Single user, single machine by default. No account, no server, no internet connection required to use it day to day. All data lives in one JSON file on your own computer.

---

## Features
**Work items**
- Status (To do / In progress / Complete) and priority (Low / Medium / High / Urgent), with automatic timestamps: moving to "In progress" stamps the start time, moving to "Complete" stamps the finish time.
- Sub-items with their own priority (a small L/M/H/U badge, click to cycle), drag-and-drop to reorder, and a simple checkbox, no independent status.
- Notes, links, and real file attachments (actual files copied in, not just shortcuts, open or remove them from the task's detail panel).
- Daily or specific-weekday recurrence. Completing a recurring task creates its next occurrence automatically and (optionally) shows a notification.
- A full history log per task, every status change with a timestamp, separate from the Started/Completed fields, which only ever hold the most recent transition.

**Organizing and finding things**
- Projects on the left, plus a mandatory "Inbox" for anything unassigned.
- A combinable filter bar: Project, Priority, Status, and a "Today" tag. Multiple tags in the same category are OR'd together (Urgent or High); different categories are AND'd (Project = Amit, and Priority = Urgent).
- Sort by Priority, Status, or Project, click the same one again to flip its direction.
- Free-text search across a task's title and notes.
- Bulk select (checkboxes on hover) to mark several tasks' status, move them to a different project, or delete them all at once.
- The app opens on your "Today" tag by default, not everything at once. The tag itself clears automatically the first time you open the app on a new day.

**Desktop-only conveniences**
- **Quick capture from anywhere**: `Ctrl`/`Cmd` + `Shift` + `Space` opens a small popup, type a task, hit Enter, it lands in Inbox, even if Palantír's main window is closed.
- **Lives in your system tray.** Closing the window hides it rather than quitting, so quick capture keeps working. Quit from the tray menu when you actually want to close it.
- **Launch at login**, toggle in Settings.
- **Daily automatic backups** (last 14 days kept), alongside manual Export/Import backup.
- **Undo for deletes**: an 8-second toast with an Undo button after deleting, single or bulk.
- **Auto-update** via GitHub Releases, checks on startup, every 4 hours, and on demand (Settings or tray menu). See "Publishing an update" below.

---

## Keyboard shortcuts
| Shortcut | Action |
|---|---|
| `Ctrl`/`Cmd` + `Shift` + `Space` | Quick capture, works from anywhere, even another app |
| `Ctrl`/`Cmd` + `K` | Command palette, jump to any task or project by typing |
| `N` | Jump to the quick-add box |
| `/` | Jump to the search box |
| `↑` / `↓` | Move the highlight between tasks in the list |
| `Enter` | Open the highlighted task |
| `1`–`4` | Set the highlighted task's priority (Low, Medium, High, Urgent) |
| Double-click a title | Rename it in place, right in the list |
| `Esc` | Close a panel, clear a selection, or clear the highlight |
| `?` | Show this list inside the app |

---

## Getting started
### Run it in development mode
```bash
npm install
npm run electron:dev
```
Opens the app in a real window with hot-reload. No setup screen, it opens straight into the console.

### Build a real installer (one-time, per machine)
```bash
npm run dist
```

Builds and packages the app. The installer lands in `release/`:
- **Windows**: `release/Palantír Setup <version>.exe`, a normal NSIS installer with a desktop shortcut.
- **macOS** / **Linux**: `.dmg` / `.AppImage` respectively, if built on those platforms.

Build on the platform you want an installer for. `npm run dist` never publishes anywhere, it's a purely local build.

---

## Publishing an update
Once the app is installed, new versions reach it through GitHub Releases rather than another manual install.

### One-time setup
1. Create a fine-grained GitHub personal access token, scoped to just the `Palantir` repo, with **Contents: Read and write**.
2. Set it as an environment variable in your terminal before publishing (never save it in a file):
   - PowerShell: `$env:GH_TOKEN="github_pat_..."`
   - cmd: `set GH_TOKEN=github_pat_...`
3. Confirm it actually took before publishing: `echo $env:GH_TOKEN` (PowerShell) or `echo %GH_TOKEN%` (cmd). This only lasts for that one terminal window.

### Each time there's a new version
1. Bump the `version` field in `package.json`.
2. With `GH_TOKEN` set in that same terminal session, run:
   ```bash
   npm run release
   ```
   This builds the app and publishes the installer plus update metadata straight to a GitHub Release.
3. Every already-installed copy picks it up automatically (within 4 hours, or immediately via Settings → "Check for updates") and shows a banner: "Version X.X.X is ready, restart to install."

### Worth knowing
- The app isn't code-signed. Expect a one-time Windows SmartScreen "Unknown Publisher" warning on a fresh install (click "More info" → "Run anyway"). Auto-updates don't re-trigger this.
- If `npm run dist` or `npm run release` fails with a Windows file-lock error (`EPERM`, rename not permitted), quit Palantír from the system tray first (not just close the window) and delete the `release/` folder before retrying, this is almost always the cause.

---

## Where your data lives
Settings (gear icon) → **Data & backups** shows the exact file path and a button to reveal it in File Explorer. It's a plain, human-readable JSON file.
- **Manual backup**: Export/Import buttons in that same panel, save or load a full copy to any file you choose.
- **Automatic backup**: a dated snapshot is saved once a day into a `backups` folder alongside the main data file, keeping the last 14 days.
- Want version history the way a git repo gives you? Export a backup whenever you like and drop it into a repo of your own by hand, no connection needed inside the app itself.

---

## Known limitations, honestly stated
- **Single machine by default.** To use it on a second computer, install there too and use Import backup to bring your data across, there's no automatic sync between machines.
- **The app quits fully via the tray menu only.** The window's close button just hides it, by design.
- **No native mobile version.** This is a desktop app, not a phone app or home-screen widget.
- **No time-of-day reminders yet.** Recurrence exists (daily/weekly), but "remind me at 9am" needs a scheduling model that hasn't been designed yet.

---

## Stack
Electron, React 19, TypeScript, Vite, Tailwind CSS, `@dnd-kit` (drag-to-reorder), `lucide-react` (icons), self-hosted fonts (works fully offline), `electron-builder` + `electron-updater` for packaging and auto-update. No backend, no network calls except checking for updates.

For architecture details, the data model, migration rules, and a running list of pitfalls already hit, see `DEVELOPMENT.md`.
