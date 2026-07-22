# Project Pulse — Implementation Plan

## 1) Executive summary
Project Pulse is a small static dashboard that reads project metadata from a JSON file (app/project-data.json) and renders a set of project cards showing status, priority, and key metrics. The goal is a runnable, accessible, responsive front-end that is easy to review and hand off. Designers are responsible for UI/UX, CSS, accessibility, and component structure; Coders are responsible for the runnable app plumbing, JSON data, and the minimal JavaScript to fetch and render the data.

This plan is file-level explicit so the Orchestrator can assign tasks to Designer and Coder without overlap, and it includes the minimal VS Code debug configuration (.vscode/launch.json) that opens the app served from ${workspaceFolder}/app.

---

## 2) Ordered implementation steps (short description + estimate)
Note: each step lists the primary owner (Designer or Coder) and files to create/update. Estimated durations are realistic for a small team; adjust if scope changes.

1. Project kickoff & repo skeleton (Coder) — 30–60 min
   - Create .vscode folder and initial launch.json (HTTP approach).
   - Create empty app/ files placeholders (index.html, styles.css, project-data.json, main.js) if not already present.
   - Deliverable: .vscode/launch.json + file stubs.
2. Designer: HTML skeleton & mount contract (Designer) — 1.5–3 hours
   - Implement app/index.html semantic structure, landmarks, and a single mount point for cards with agreed IDs/classes.
   - Deliverable: index.html with markup placeholders and comments describing the DOM/data contract.
3. Coder: JSON schema + seed data (Coder) — 30–60 min
   - Create app/project-data.json with a documented schema and two example projects.
   - Deliverable: valid JSON + brief schema comment block (or separate docs) describing fields and enums.
4. Designer: Visual design & responsive CSS (Designer) — 2–4 hours
   - Implement app/styles.css: variables, layout grid, responsive behavior, focus styling, status/priority tokens, and accessible color choices.
   - Deliverable: styles.css that styles the mount-point structure defined in index.html and the data/DOM contract.
5. Coder: Data fetch + render logic (Coder) — 2–4 hours
   - Implement app/main.js to fetch project-data.json, safely render project cards into the mount point, and show loading / error UIs.
   - Deliverable: main.js that renders cards and handles errors; uses textContent (no innerHTML for untrusted data).
6. Integration & accessibility pass (Designer then Coder) — 1–2 hours
   - Designer reviews rendered DOM, adjusts markup or CSS for accessibility; Coder updates rendering to match any final DOM adjustments.
   - Deliverable: final accessible component structure, keyboard interactions, and ARIA attributes implemented.
7. Debug/launch integration (Coder) — 30–90 min
   - Add .vscode/tasks.json (recommended) to start static server automatically; update launch.json to optionally run preLaunchTask.
   - Deliverable: launch.json + tasks.json that open the running app in the browser with cwd set to ${workspaceFolder}/app.
8. QA, testing & fixes (Designer + Coder) — 1–3 hours
   - Manual functional checks, accessibility checks (axe/Lighthouse), mobile/responsive checks, fix issues discovered.
   - Deliverable: list of issues resolved and confirmation of acceptance checklist.
9. Final docs & handoff (Coder) — 30–60 min
   - Save plan (this file) to docs/project-pulse-plan.md, update README with run instructions.
   - Deliverable: docs/project-pulse-plan.md committed by Orchestrator; README updated.

Total conservative time: ~9–18 hours depending on polish and fixes.

---

## 3) File assignments (required + recommended)
Each file is assigned to a single owner to avoid edit conflicts. Acceptance criteria are testable.

Important shared contract (Designer + Coder): the mount point id and class names the JS will use. Designer must implement these names in index.html; Coder must target them in main.js. Contract (example):
- Mount point: <main id="project-list" aria-live="polite"></main>
- Card root class: .project-card
- Title: .project-title
- Status badge: .project-status (data-status attribute)
- Priority badge: .project-priority (data-priority attribute)
- Metrics container: .project-metrics
- Each project rendered as an <article class="project-card" role="listitem"> inside a container with role="list" or a <ul>

Required files (explicit):

- app/index.html — Designer (owner)
  - Implement:
    - Semantic page structure (head, header, main, footer).
    - Mount point element: <main id="project-list" aria-live="polite"></main> and a small loading placeholder.
    - Include link to styles.css and <script src="main.js" defer></script>.
    - Accessible controls placeholders (search/filter UI structure — no JS required; data attributes to be used by JS later).
    - Comments documenting the DOM/data contract and any class/id expectations.
  - Acceptance criteria:
    - Contains the mount point with exact id "#project-list".
    - Includes links to styles.css and main.js (deferred).
    - Landmarks and headings present (header and main). No visual styling required at this stage, but structure must be valid HTML5.

- app/styles.css — Designer (owner)
  - Implement:
    - CSS variables for color, spacing, type scale.
    - Responsive grid for project cards (desktop 3-col, tablet 2-col, mobile 1-col).
    - Visual tokens for statuses (on-track/at-risk/off-track) and priorities (low/med/high/critical).
    - Visible focus outlines, prefers-reduced-motion support, and print-friendly styles.
  - Acceptance criteria:
    - Grid responds at typical breakpoints (>=1024px: 3 columns; 768–1023px: 2 columns; <768px: 1 column).
    - Status and priority color contrast meets WCAG AA.
    - Focus is visible for keyboard navigation.
    - Documented style variables at file top.

- app/project-data.json — Coder (owner)
  - Implement:
    - JSON schema adherence and seed data (2 example projects).
    - Valid JSON (no trailing commas).
    - Short schema documentation (comment in docs or separate file; JSON cannot contain comments).
  - Acceptance criteria:
    - Valid JSON file that fetches successfully from a local static server.
    - Matches agreed schema (sample schema below).
  - Example JSON (put exactly this content into file):
    ```json
    [
      {
        "id": "proj-001",
        "name": "Website Redesign",
        "description": "Migrate company site to the new design system.",
        "status": "at-risk",
        "priority": "high",
        "metrics": {
          "percentComplete": 62,
          "tasksCompleted": 31,
          "totalTasks": 50,
          "healthScore": 58
        },
        "owner": { "name": "Alice Gomez", "email": "alice@example.com" },
        "updatedAt": "2026-07-21T14:22:00Z",
        "tags": ["design", "frontend"]
      },
      {
        "id": "proj-002",
        "name": "Mobile App Launch",
        "description": "Publish v1.0 to App Stores with analytics.",
        "status": "on-track",
        "priority": "critical",
        "metrics": {
          "percentComplete": 88,
          "tasksCompleted": 44,
          "totalTasks": 50,
          "healthScore": 86
        },
        "owner": { "name": "Dev Team", "email": "devs@example.com" },
        "updatedAt": "2026-07-20T09:10:00Z",
        "tags": ["mobile", "release"]
      }
    ]
    ```

- .vscode/launch.json — Coder (owner)
  - Implement:
    - Launch configuration that opens the app served at http://localhost:5500 (recommended) and sets cwd to ${workspaceFolder}/app.
    - Explain: using an HTTP server is required so fetch() to project-data.json works reliably (file:// requests are blocked by CORS in many browsers).
  - Acceptance criteria:
    - The config opens the app in the browser and uses cwd exactly: ${workspaceFolder}/app.
  - Recommended content (example to place in .vscode/launch.json):
    ```json
    {
      "version": "0.2.0",
      "configurations": [
        {
          "name": "Launch Project Pulse (http)",
          "type": "pwa-chrome",
          "request": "launch",
          "url": "http://localhost:5500",
          "webRoot": "${workspaceFolder}/app",
          "cwd": "${workspaceFolder}/app"
        }
      ]
    }
    ```
    - Note: This assumes a static server is running on port 5500. See recommended tasks.json below for automatic server start.

Recommended additional files (Coder owners unless otherwise noted):

- app/main.js — Coder (owner) — REQUIRED recommended file (not in the user's required list but strongly recommended)
  - Implement:
    - Fetch app/project-data.json, render accessible DOM according to contract, show loading and error states, safe rendering (textContent).
    - Minimal sorting/filtering API (optional) or provide hooks for Designer controls.
    - Handle missing fields with sensible defaults.
  - Acceptance criteria:
    - When served via static server and launch.json used, the page renders the two example projects without console errors.
    - Error message displays if fetch fails.

- .vscode/tasks.json — Coder (owner) — recommended
  - Implement a background task to start a static server for the app folder, e.g. using python or npx http-server. Example (task must be tuned to environment):
    ```json
    {
      "version": "2.0.0",
      "tasks": [
        {
          "label": "Start static server",
          "type": "shell",
          "command": "npx http-server ${workspaceFolder}/app -p 5500",
          "isBackground": true,
          "problemMatcher": []
        }
      ]
    }
    ```
  - Acceptance criteria:
    - Running the task produces a server on http://localhost:5500 and serves project-data.json.

- package.json (optional) — Coder
  - Add convenient scripts like `start` to run a static server (if the team prefers npm scripts).

Notes to prevent conflicts:
- Designer owns index.html and styles.css exclusively.
- Coder owns project-data.json, main.js, and .vscode configurations.
- If a Designer wants small DOM changes (class names, new ids), they must update the DOM contract comment in index.html. Coder will follow that contract.

---

## 4) Dependencies (blocking items)
- main.js (Coder) depends on:
  - index.html mount point (Designer) — blocking: the id/class names must exist and match the data contract.
  - project-data.json (Coder) — blocking only for the live demo (main.js can have a fallback embedded sample to avoid blocking while JSON is being finalized).
- styles.css (Designer) depends on index.html class names and the data contract.
- launch.json (Coder) depends on a static server being available; tasks.json or a separate server run is required. Without a server, fetch() will fail in browsers when using file://.
- Integration & accessibility pass depends on first delivery of both index.html and styles.css and an initial main.js render.

---

## 5) Parallel work decisions
Work that can run in parallel (no conflict if teams follow the DOM/data contract):
- Designer: index.html + styles.css (structure + visual system) — can be done together.
- Coder: project-data.json + main.js skeleton + .vscode/launch.json — can be done in parallel with Designer's work using the shared DOM contract.
Why safe: Designer defines the mount point id and class names up front; Coder uses those fixed names to render content. This keeps simultaneous edits to separate files.

Work that must be sequential:
- Full integration (functional demo) requires both index.html and project-data.json to exist and match the contract before main.js finalizes rendering logic.
- Accessibility & interaction polish should be a Designer-first pass (update markup & CSS) followed by Coder updates to JavaScript to match any final DOM tweaks.

---

## 6) Validation expectations
A. Manual functional checks (Coder + Designer)
- Start the static server in the app folder (see launch.json & tasks.json). Recommended commands:
  - npx http-server ./app -p 5500
  - OR python -m http.server 5500 --directory app
- Open http://localhost:5500 (or use VSCode "Launch Project Pulse (http)" debug).
- Confirm:
  - Page loads and shows 2 project cards from project-data.json.
  - Each card displays name, status (color-coded), priority, and metrics (percentComplete or health).
  - No fetch/XHR console errors; no CORS errors.
  - Loading state shows while fetch is in progress and disappears after render.
  - Error state visible if project-data.json is unreachable.

B. Accessibility checks
- Keyboard:
  - Tab through interactive elements (search, filters, links, card actions). Visible focus ring present.
  - Cards reachable and actionable via keyboard.
- Screen reader:
  - Use NVDA/VoiceOver to confirm header/main landmarks and that project cards are announced as list/listitems or articles.
- Automated:
  - Run Lighthouse and axe (or the WAVE extension).
  - Color contrast: status/priority tokens meet WCAG AA (minimum 4.5:1 for normal text; 3:1 for large text).

C. Edge functional tests
- Missing fields: if a project lacks metrics or owner, UI shows fallback (e.g., "—" or "Not assigned").
- Unknown enum values: show label "Unknown" and a neutral style.
- Long text wrap: project name wraps and the card does not overflow layout.
- Large dataset: page remains usable with 50–100 projects (render performance acceptable; consider lazy rendering only if dataset grows).

D. Validation for .vscode/launch.json
- Steps to validate:
  1. Start the static server manually (or via tasks.json).
  2. In VS Code, run the "Launch Project Pulse (http)" configuration.
  3. Browser should open http://localhost:5500 and display index.html.
  4. Verify fetch requests for /project-data.json succeed in DevTools Network tab.
- If you prefer a file:// approach (not recommended), replace the launch.json with a "file" attribute (but note fetch of relative JSON files often fails under file:// due to CORS; thus HTTP is recommended).

---

## 7) Edge cases and risks (with mitigations)
- Fetch blocked on file:// or CORS:
  - Mitigation: require an HTTP server (tasks.json + launch.json). Document in README.
- Invalid/malformed JSON:
  - Mitigation: main.js should catch fetch/parse errors, show a helpful UI message, and fall back to a minimal embedded dataset if desired.
- Missing/extra fields in JSON:
  - Mitigation: main.js uses defaults for missing fields and ignores unknown fields safely.
- Long strings overflowing card layout:
  - Mitigation: CSS must handle overflow (wrap/truncate with title attribute for full text).
- Accessibility regressions (non-contrasting colors, invisible focus):
  - Mitigation: Designer to use accessible token palette and check with axe/Lighthouse; ensure focus styles are visible.
- XSS from JSON:
  - Mitigation: always insert content using textContent or proper sanitization. Never set innerHTML from raw JSON.
- Browser compatibility:
  - Mitigation: target evergreen browsers; use polyfills only if required by the user.
- Conflicting edits between Designer and Coder:
  - Mitigation: Strict ownership of files; publish the DOM/data contract in index.html comments so both parties have a single source of truth.

---

## 8) Validation expectations (summary checklist)
Before declaring the feature complete:
- [ ] index.html present with mount point id="#project-list" and semantic landmarks.
- [ ] styles.css present and responsive with accessible color contrast.
- [ ] project-data.json valid and fetchable from http://localhost:5500/project-data.json.
- [ ] main.js renders project cards and handles loading/error states; no console errors.
- [ ] VS Code launch configuration opens the app (use launch.json with cwd ${workspaceFolder}/app).
- [ ] Keyboard navigation complete; focus visible.
- [ ] Automated accessibility audit (axe/Lighthouse) passes with no critical issues.
- [ ] README updated with run instructions (how to start the static server and which launch configuration to use).

---

## 9) Open questions for the Orchestrator / product owner
Please confirm or answer these so the team can proceed without blockers:

1. Branding & visual guidance
   - Any color palette, fonts (Google fonts), or brand assets to use?
2. Feature scope
   - Do you want filtering/sorting/search in this sprint, or is read-only rendering sufficient?
3. Data fields
   - The sample schema includes name, description, status, priority, metrics, owner, tags, updatedAt. Are additional fields required (images, links, due dates)?
4. Server preference
   - Should we rely on `npx http-server`, `python -m http.server`, Live Server extension, or add a small npm dev dependency and start script?
5. Target browsers & accessibility baseline
   - Confirm target browsers and required WCAG level (AA recommended).
6. CI / automated checks
   - Do you want automated Lighthouse or accessibility checks added to CI later?
7. Interaction design
   - Should a whole card be clickable (navigating to a project detail) or should there be an explicit action button? Designer to implement; please confirm preference.

---

## Appendix — Example JSON schema (short)
Recommended schema (informal):

- id: string
- name: string
- description: string
- status: enum ["on-track", "at-risk", "off-track", "complete", "paused"]
- priority: enum ["low", "medium", "high", "critical"]
- metrics: { percentComplete: number (0–100), tasksCompleted: int, totalTasks: int, healthScore: 0–100 }
- owner: { name: string, email: string }
- updatedAt: ISO 8601 string
- tags: array of strings

(Example data included earlier in the project-data.json sample.)

---

If you want, I can now:
- Produce a minimal checklist of exact DOM class/id names for the contract in one small file so Designers and Coders can start in parallel, or
- Create a ready-to-paste .vscode/launch.json + tasks.json snippet tuned to your preferred server command.

Which would you like me to prepare next?