# Architecture

SoftPlans is a client-side application written in HTML, CSS and JavaScript ES modules. Vite bundles and minifies the production site. The UI uses DOM APIs and retains unchanged task and group nodes through keyed caches.

## Module boundaries

| Module                        | Responsibility                                                                              |
| ----------------------------- | ------------------------------------------------------------------------------------------- |
| `src/app.js`                  | Own the in-memory workspace and selection state; coordinate rendering and user events       |
| `src/core/tasks.js`           | Select tasks and build ordered view sections without touching the DOM                       |
| `src/core/dates.js`           | Validate local calendar dates, format dates and calculate scheduling boundaries             |
| `src/core/search.js`          | Normalize queries, match task metadata and calculate safe highlight offsets                 |
| `src/core/storage.js`         | Validate the workspace schema, read legacy storage, and serialize backups                   |
| `src/core/workspace-store.js` | Track the last successfully saved snapshot and reject failed writes                         |
| `src/core/demo-data.js`       | Create initial sample data with fresh IDs and dates relative to today                       |
| `src/ui/`                     | Encapsulate pickers, completion animation, group sorting, notifications and backup controls |
| `src/i18n/`                   | Supply matching translation keys for all three languages                                    |
| `src/styles/`                 | Import design tokens and component, theme, desktop and responsive styles in a fixed order   |

## Workspace lifecycle

1. Read the versioned workspace from `softplans.workspace.v1`.
2. If it is absent, read the earlier `softplans.tasks.v1` and `softplans.groups.v1` keys. Normalize valid legacy entries, recognized colors and orphaned group references without modifying the original keys.
3. If there is no current or legacy workspace, the application creates and saves the initial sample tasks and groups once. Saved workspaces, including intentionally empty ones, keep their existing contents. Unreadable or unavailable storage never triggers sample initialization.
4. Validate and save tasks and groups together in one `localStorage.setItem` call after an edit. The new workspace key takes precedence over legacy keys.
5. Commit the in-memory snapshot only after a successful write. Restore the previous snapshot when a save fails; keep form drafts available and report the error.
6. Listen for storage events from other tabs, retaining the last good snapshot if newly stored data is unreadable. The latest saved workspace wins; there is no conflict-merging service.

Preferences use separate `softplans.language`, `softplans.theme` and `softplans.sidebar` keys. English and light mode are the defaults. Existing saved preferences remain in use.

## Data format

The workspace has `version: 1`, `groups` and `tasks` arrays. Groups contain unique string IDs, names and an allowed palette color. Tasks contain:

| Field                   | Type                                                 |
| ----------------------- | ---------------------------------------------------- |
| `id`, `text`            | Non-empty strings                                    |
| `completed`, `favorite` | Booleans                                             |
| `completedAt`           | Local `YYYY-MM-DD` date or `null`                    |
| `dueDate`               | Local `YYYY-MM-DD` date or `null`                    |
| `dueTime`               | 24-hour `HH:MM` value or `null`; requires a due date |
| `groupId`               | Existing group ID or `null`                          |

Backup files add `app: "SoftPlans"` and an ISO `exportedAt` timestamp. Import checks the format version, unique IDs, group references and schedule fields before any write. Files larger than 5 MiB are rejected. Importing into a populated workspace requires explicit confirmation.

Unreadable current storage is preserved until the user explicitly imports a valid backup or starts fresh. A recovery download contains the raw saved strings for manual repair, rather than pretending they are a valid backup.

## Development tooling

The package override pins the patched minimatch major used by ESLint. Keep it until ESLint and its config-array dependency adopt the patched version directly; recheck linting and `npm audit` when changing it.

## Calendar behavior

Task dates are local calendar values, not UTC timestamps. Local date parsing validates real days and handles years 1–99 without JavaScript's default 1900 offset. Adding days uses calendar arithmetic across daylight-saving transitions. Month presets clamp to the last valid day of the target month.

A task becomes overdue after its scheduled minute, or on the day after an untimed due date. Completion history stores the local day when the task was completed.

## Rendering and assets

User-supplied task text, group names and search matches are inserted as text nodes. `innerHTML` is used only for constant interface icons. Search highlighting maps normalized matches back to the original Unicode string.

Production assets use relative paths through Vite's `base: "./"`. The public manifest and icons remain relative to the deployed app directory. There is no service worker or background synchronization. The app needs a server for its initial module and stylesheet loads; it does not promise offline installation.

## Validation

Unit tests cover date boundaries, migration, failed writes, workspace relationships, backup validation, filtering, ordering, search and translation parity. Playwright exercises user-visible workflows in desktop Chromium, mobile Chromium and WebKit against a production build under `/SoftPlans/`. axe checks representative views and dialogs in light and dark themes. Screenshots are illustrative documentation, not pixel-difference tests.
