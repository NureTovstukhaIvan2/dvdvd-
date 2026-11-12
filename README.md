<div align="center">

<img src="public/icons/icon.svg" width="76" height="76" alt="SoftPlans icon" />

# SoftPlans

A calm place for your plans and tasks.

A responsive task planner with custom groups, scheduling, three interface languages and light and dark themes. Built with vanilla JavaScript, with your workspace saved in your browser.

**[Live demo](https://ivam2280.github.io/SoftPlans/)** · [Getting started](#getting-started) · [Screenshots](#screenshots) · [Deployment](#deployment)

![JavaScript](https://img.shields.io/badge/JavaScript-ES_modules-f7df1e?logo=javascript&logoColor=202020)
![Vite](https://img.shields.io/badge/Vite-8-646cff?logo=vite&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-6e64c2)

</div>

## Features

- **Plan your day:** All, Today, Scheduled, Important and Completed views, with overdue tasks and upcoming dates grouped automatically.
- **Organize your workspace:** create and rename color-coded groups, then reorder them by dragging or with the keyboard.
- **Manage tasks:** add, edit, flag, complete, restore and delete tasks; assign a group, date and optional time.
- **Find anything:** search across task text, group names, dates and times, including completed tasks, with highlighted matches.
- **Use it anywhere:** a desktop sidebar, tablet layouts, and mobile navigation with a floating task composer, swipe actions and long-press menus.
- **Make it yours:** English, Ukrainian and Russian interfaces, persistent light and dark themes, and a collapsible desktop sidebar.
- **Keep a copy:** export and restore validated JSON backups; unreadable storage offers a recovery download before reset.

A new workspace starts empty. Select **Try demo** to explore sample tasks and groups. Demo data is optional and never replaces an existing workspace.

## Screenshots

Real browser captures of the optional demo workspace. Screenshots in each row use the same viewport size.

### Desktop

|                                                     Workspace                                                      |                                                           Schedule                                                           |
| :----------------------------------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------------------------: |
| <img src="docs/screenshots/desktop-overview.webp" alt="SoftPlans workspace with custom task groups" width="640" /> | <img src="docs/screenshots/desktop-scheduled.webp" alt="Scheduled tasks organized by date and overdue status" width="640" /> |

|                                                         Date picker                                                          |                                              Dark theme                                              |
| :--------------------------------------------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------------------------: |
| <img src="docs/screenshots/desktop-date-picker.webp" alt="Calendar with date presets and keyboard navigation" width="640" /> | <img src="docs/screenshots/desktop-dark.webp" alt="Scheduled tasks in the dark theme" width="640" /> |

### Mobile

|                                                           Workspace                                                           |                                                          Task editor                                                           |
| :---------------------------------------------------------------------------------------------------------------------------: | :----------------------------------------------------------------------------------------------------------------------------: |
| <img src="docs/screenshots/mobile-overview.webp" alt="Mobile workspace with group chips and bottom navigation" width="320" /> | <img src="docs/screenshots/mobile-task-editor.webp" alt="Mobile task editor with date, time and group controls" width="320" /> |

### Tablet

|                                            Today                                             |                                                     Backups                                                      |
| :------------------------------------------------------------------------------------------: | :--------------------------------------------------------------------------------------------------------------: |
| <img src="docs/screenshots/tablet-today.webp" alt="Today's tasks on a tablet" width="640" /> | <img src="docs/screenshots/tablet-backups.webp" alt="Export and import backup dialog on a tablet" width="640" /> |

## Getting started

Use **Node.js 24 or newer** and npm. The repository includes an `.nvmrc` for Node 24.

```bash
git clone https://github.com/Ivam2280/SoftPlans.git
cd SoftPlans
npm ci
npm run dev
```

Open **http://127.0.0.1:3000/**. No environment variables, API keys, database or account are required.

To preview the production build:

```bash
npm run build
npm run preview
```

Open **http://127.0.0.1:4173/**. Use the development or preview server when running locally so the browser can load ES modules correctly.

## Commands

| Command               | Purpose                                                             |
| --------------------- | ------------------------------------------------------------------- |
| `npm run dev`         | Start the Vite development server                                   |
| `npm run build`       | Build the production site in `dist/`                                |
| `npm run preview`     | Serve the production build locally                                  |
| `npm run check`       | Run ESLint, formatting checks and unit tests                        |
| `npm run test:e2e`    | Build and test the app in Chromium, mobile Chromium and WebKit      |
| `npm run format`      | Format source files and documentation                               |
| `npm run screenshots` | Refresh the eight README screenshots against the development server |
| `npm run icons`       | Generate PNG app icons from the SVG source                          |

Before running browser tests or regenerating screenshots and icons, install the browser engines once:

```bash
npx playwright install chromium webkit
```

On Linux, use `npx playwright install --with-deps chromium webkit` to install the required system libraries too.

## Project structure

```text
.github/workflows/deploy.yml   Checks and GitHub Pages deployment
public/                       Manifest and app icons, copied to the build
src/
  app.js                      Application state, events and UI coordination
  core/                       Dates, filters, search, storage and demo data
  i18n/                       English, Ukrainian and Russian dictionaries
  ui/                         Date picker, animation, sorting and backup UI
  styles/                     Tokens, components, themes and responsive rules
scripts/                      Reproducible screenshots and icon generation
tests/
  unit/                       Data validation, dates, search and task logic
  e2e/                        User flows, accessibility and deployment checks
docs/
  screenshots/                README images
  architecture.md             State, persistence and module boundaries
index.html                    Application entry point
vite.config.js                Development and production configuration
```

The app uses native browser APIs and has **no runtime package dependencies**. Vite, ESLint, Prettier, Playwright and axe are development tools.

## Data and backups

Tasks, groups and preferences stay in `localStorage` for the current browser and website origin. There is no account, cloud sync, server database, analytics or notification service. Date and time labels organize tasks; they do not send reminders. Google Fonts supplies the interface fonts.

Open **Backups** in the desktop toolbar or mobile settings to export a JSON file or import a previous SoftPlans backup. Import validates the file before making changes and asks for confirmation before replacing a populated workspace. Keep an export before clearing browser data or moving to another device or domain.

Tabs on the same origin refresh when another tab saves a workspace. Simultaneous edits use the latest saved snapshot. Earlier SoftPlans task and group storage is read automatically and migrated on the first successful edit. A failed save keeps the previous saved data and displays an error.

A recovery download preserves unreadable storage for manual inspection. It is separate from a validated backup and cannot be imported directly.

## Interaction and accessibility

- Select a task's text to edit it; use its checkbox to complete or restore it and its flag to mark it important.
- Right-click a task or group for additional actions. On touch screens, long-press instead; swipe a task right to complete or restore it and left to delete it.
- Drag groups to reorder them. With a group focused, use **Alt + arrow keys** to move it.
- Press **/** outside a text field to focus search. Press **Escape** to dismiss open controls.
- In the calendar, use arrow keys to move between days and **Enter** to apply the date. The time picker supports arrow navigation and direct `HH:MM` entry.

The interface includes labeled controls, keyboard focus indicators, modal focus handling, live status messages and reduced-motion support. Automated axe checks cover the main views and dialogs in both themes; they complement manual keyboard and layout checks.

## Deployment

The included workflow builds and publishes **`dist/`** to GitHub Pages after linting, formatting, unit tests and browser tests pass.

1. Upload this repository's files to the root of your GitHub repository, including the hidden **`.github`** directory and `package-lock.json`. When updating an existing repository, replace its previous source structure and remove obsolete workflows.
2. In **Settings → Pages → Build and deployment**, select **Source: GitHub Actions**.
3. Push to `main` or `master`, or run **Checks and GitHub Pages** from the Actions tab.
4. Wait for the deployment job to finish, then open the URL shown in the `github-pages` environment.

Pull requests run the checks without publishing. No personal access token or custom secret is needed. The deployment checks that Pages uses GitHub Actions and reports the exact setting to change if it does not.

Vite's relative asset base supports both a domain root and repository paths such as `/SoftPlans/`. Browser tests serve the compiled app under `/SoftPlans/` and verify local asset and manifest URLs. When changing the repository name, update the clone and live-demo links in this README.

If an old version appears, confirm that the latest workflow succeeded and refresh the page with the browser cache disabled. Pages must publish the compiled artifact through this workflow. Remove any older Pages workflow that publishes a different folder.

For other static hosts, run `npm run build` and publish `dist/`.

## Development

See [CONTRIBUTING.md](CONTRIBUTING.md) for the verification workflow and [architecture notes](docs/architecture.md) for the module layout and storage format.

## Author and license

Created by **[Ivan Tovstukha](https://github.com/Ivam2280)**. Released under the [MIT License](LICENSE).
