# HANDOFF — Pathways Dashboard
_Written: 2026-06-05_

## Last Stable Commit
Phase 5 — add Engaging Humor 2nd path, two-section layout (active/completed)

## What Was Done

### Phase 5 — Two-section layout + Engaging Humor (2nd) path (2026-07-24)
- Added new `engaging-humor-2` entry to `PATHS_DATA`: same levels/projects as `engaging-humor`, `defaultStatus: '—'`, `isNew: true`, all electives `e(name, false)` — nothing added to `initDefaults()`, so it renders fully blank/not-started
- Split the single `#paths-scroll` row into two sections under `#paths-section`:
  - `#active-paths-scroll` ("Active Paths" heading) — non-scrolling `.paths-row` (wraps), holds Engaging Humor (2nd) then Basic Training for Toastmasters
  - `#completed-paths-scroll` ("Completed Paths" heading) — `.paths-row.scrollable`, holds the 5 completed paths: Engaging Humor (1st), Presentation Mastery, Persuasive Influence, Team Collaboration, Innovative Planning
  - Section membership + order is explicit via `ACTIVE_PATH_IDS` / `COMPLETED_PATH_IDS` arrays + `pathsById()`, not inferred from array position
- `renderPath()` now has a third badge state: `isNew` → badge class `new` ("NEW" text, `#001f3f` background/white text) and header gets `.path-col-header.new-path` (`#001f3f` solid, distinct from the existing `--blue-grad` used for in-progress/completed headers)
- Renumbered the `PATHS_DATA` block comments (1–7) to keep them sequential after inserting the new path
- Click-delegation listener moved from `#paths-scroll` (removed) to `#paths-section` (parent of both new rows) so one listener still covers both sections

## Earlier Phases

### Strategy B Phase 1 — Excel-to-static-HTML generator (2026-06-05)
- `generate-dashboard.py`: reads `pathway-record-master.xlsx` (MY RECORD sheet via `wb.sheetnames[2]`) using openpyxl
- Parses PATH / CATEGORY / LEVEL / PROJECT / KIND / STATUS columns
- Skips section-header rows (path = 'PATH' or project in ('Current Path', ...))
- Groups rows: path → level → [rows]
- Level COMPLETE = all Required=COMPLETE + ≥1 Elective=COMPLETE (if electives exist)
- Path status: COMPLETE / IN PROGRESS / NOT STARTED
- Progress (done/total): Required rows + 1 elective slot per level with electives
- Sort: IN PROGRESS first, then COMPLETE, then NOT STARTED; within each: current < legacy < vintage
- Outputs `bond-pathways-dashboard.html`: FTH-compatible (no html/head/body), pure CSS, no JS
- Header colors: current+InProgress=#004165, current+other=#0065a0, legacy=#772432, vintage=#4A3468
- Status pills: COMPLETE=maroon filled, IN PROGRESS=yellow black-text, blank=em dash gray
- Enhancement rows (Meeting Role, Ed Series): italic amber smaller text
- Elective rows: E badge prefix
- 5 achievements: Engaging Humor, Persuasive Influence, Presentation Mastery, Innovative Planning, Team Collaboration
- 929 lines, ~104KB

**Next step:** Upload `bond-pathways-dashboard.html` to FTH subpage



### Scrollbar (Phase 4d — post 4e patch)
- `main`: `overflow-x: hidden` to contain layout
- `#paths-section`: `overflow: hidden` to clip child overflow
- `#paths-scroll`: `overflow-x: scroll` (not auto), `width: 100%`, `min-width: 0`
- Added `::-webkit-scrollbar` rules: 8px height, `#004165` thumb, `#772432` hover, `#e0e0e0` track

### Layout (Phase 4e)
- Reverted fixed-viewport layout — `body` is simple block flow, no `height: 100vh` or `overflow: hidden`
- ONE horizontal scrollbar: `#paths-scroll` has `overflow-x: auto`; no per-column overflow
- ONE vertical scrollbar: default browser scroll — achievements section and footer are fully accessible
- `.path-col` uses `flex: 0 0 280px; min-width: 280px` — columns compress slightly before triggering horizontal scroll
- Removed all `min-height: 0`, flex column, and overflow constraints from path-col and path-col-body

### Header (Phase 4e)
- Enhancement toggle moved back into `<header>` (right side, `justify-content: space-between`)
- `#enhancement-bar` div removed entirely
- Toggle button restored to white/transparent style for dark blue header background

### Section Heading (Phase 4e)
- Inline `.section-note` span replaces the separate `.section-helper` paragraph
- Note text in parentheses, `Source Sans 3` 0.72rem, muted color, inherits h2 line
- `h2` margin-top set to `1rem` for comfortable spacing in natural flow

### Achievements (Phase 4e)
- `.achievement-badge` changed from green to `var(--maroon-grad)` with white text
- Matches `.status-pill.complete` — Montserrat, uppercase, letter-spacing

### Earlier Phases
- Phase 4d: sticky header layout attempt (reverted in 4e)
- Phase 4c: brand pills, In Progress state, ID in header, completion date removed
- Phase 4b: real path data, electives, enhancement flag logic
- Phase 4a: CSS polish — TM brand colors, card headers, fonts
- Phase 3: path data + dashboard structure

## Next Step
GitHub Pages is live at bond7010.github.io/pathways-dashboard/pathways-dashboard.html. FTH embedding is abandoned — pathways-dashboard.html is the canonical file. Next: verify the new two-section layout renders correctly in a real browser (not yet visually confirmed) and share the link with the club.

## Danger Zones
- `renderLevel` takes `(path, levelIdx, level)` — path object needed for type/defaultStatus
- Pill click handler attached once in DOMContentLoaded (event delegation on `#paths-section`, covers both `#active-paths-scroll` and `#completed-paths-scroll`) — do NOT re-attach on renderAll
- Active/Completed section membership is explicit (`ACTIVE_PATH_IDS` / `COMPLETED_PATH_IDS`), not inferred — adding a path to `PATHS_DATA` without adding its `id` to one of these arrays means it won't render anywhere
- `initDefaults()` must run AFTER `loadStore()` and BEFORE `renderAll()`
- Elective key format is slug-based: `{pathId}.L{n}.elec.{slug}` — numeric elec keys are stale
- CSS `:has()` selector used for elec-badge coloring — requires Chrome 105+, Firefox 121+, Safari 15.4+
