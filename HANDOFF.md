# HANDOFF — Pathways Dashboard
_Written: 2026-06-05, updated 2026-08-18_

## Last Stable Commit
Phase 6 P3 — polish, localStorage QA, Evaluation+Feedback 3-form fix, mobile

## What Was Done

### Phase 6 — Pick-a-path rebuild (2026-08-18)
Replaced the old two-section layout (hardcoded `PATHS_DATA`, 7 paths, one
instance each) with a three-section, multi-instance system. `PATHS_DATA` and
its render path (`renderPath`/`renderLevel`/`ACTIVE_PATH_IDS`/etc.) are now
dormant — left in place but unused, since it was explicitly preserved rather
than deleted when the new system was built alongside it.

- **Section 1 — Pick Your Path** (`#pick-path-section`): a `card-grid` of all
  13 canonical paths (6 current / 5 legacy / 2 vintage — names fixed twice
  after early guesses didn't match the canonical Excel/JSON source), each a
  card with `+ Activate` / `+ Completed` buttons gated by `confirm()`.
- **Sections 2 & 3 — Active / Completed Paths**: instance-based. Every "add"
  click creates `{ pathId, instanceId, pathName }` in `v6Store` (persisted to
  localStorage key `pathwaysDashboardV6`), and each instance renders as a
  full `path-card` with a horizontal-scrolling row of level boxes
  (`.level-scroll-row` / `.level-box`), reusing the original pill-toggle
  infrastructure (`store`, `STORAGE_KEY`, `getStatus`/`cycleStatus`/`pillClass`)
  with new keys shaped `v6_{pathId}_{instanceId}_{levelIndex}_{projectIndex}`.
  Multiple instances of the same path are numbered "(2)", "(3)", ... in add
  order (`instanceDisplayName`).
- **Path data**: `PATH_CATALOG` (~1000 lines) holds real levels/projects/
  enhancements for all 13 paths, generated from `pathways.json` (downloaded
  from Bond7010/Toastmasters-Pathways-JSON-Format) rather than hand-retyped —
  the old `PATHS_DATA` only covered 6 of the 13 canonical path IDs and uses a
  different ID scheme, so it couldn't be reused directly.
- **Eval form buttons**: `evalFormUrl` per project, fuzzy-matched (stemmed
  word-bag containment, not exact string match) against the real filenames in
  `toastmasters-pathways-app`'s `eval-forms/current-paths/`,
  `eval-forms/basic-training/`, and `eval-forms/communication-series/`
  directories — 438/456 projects matched automatically. "Evaluation and
  Feedback" (Level 1, every current/legacy path) is a special case: it has no
  single form, so it renders three labeled buttons (First/Second/Evaluator
  Speech) via `project.evalForms` instead of the usual single-icon
  `project.evalFormUrl` button.
- **Remove**: confirm-gated; deletes the `v6Store` entry AND every
  `v6_{pathId}_{instanceId}_*` key from `store` (localStorage QA requirement —
  the first cut of this only removed the `v6Store` entry and left orphaned
  pill keys behind).
- **NEW badge**: shown when no pill under an instance has ever left its blank
  default. Because pill clicks patch the DOM directly (no full card
  re-render, for performance), the click handler also patches the NEW badge
  in place — a first version of this left the badge stale after the first
  click, caught by a jsdom functional test, not manual inspection.
- **Visual polish**: card header background now keys off category
  (`path-card-header--current/legacy/vintage`, blue/maroon/#6b7280), category
  pill + optional NEW pill in the header, remove button restyled red
  (#c0392b) with `margin-left: auto`, empty-state messages centered/italic/
  gray, `h2`s got a maroon left-border accent, Legacy Paths group got an
  italic "no longer available for new purchase on Base Camp" note.

## Earlier Phases

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
GitHub Pages is live at bond7010.github.io/pathways-dashboard/pathways-dashboard.html. FTH embedding is abandoned — pathways-dashboard.html is the canonical file. Phase 6 (three-section rebuild) is functionally complete and covered by jsdom scripted tests, but has not yet been eyeballed in a real browser — do that before announcing it to the club. Worth a look: the old dormant `PATHS_DATA`/`renderPath` code (~500 lines) could be deleted now that `PATH_CATALOG` fully supersedes it, if nothing else ends up depending on it.

## Danger Zones (Phase 6 — current architecture)
- `PATH_CATALOG` is the single source of truth for both the picker (Section 1, grouped via `PICKER_GROUP_ORDER` + `.category`) and the cards (Sections 2 & 3) — do not reintroduce a separate picker-only path list, it will drift.
- Pill state keys for v6 cards are `v6_{pathId}_{instanceId}_{levelIndex}_{projectIndex}`, stored in the *same* `store`/`STORAGE_KEY` object as the old dormant system. `removePathInstance` deletes by prefix match (`v6_{pathId}_{instanceId}_`) — if the key format ever changes, that prefix match must change with it or removed instances will leak orphaned keys.
- The pill click handler patches the clicked pill's DOM directly instead of calling `renderV6Sections()` (avoids losing scroll position / full re-render flicker). Any per-card derived UI that depends on pill state — currently just the NEW badge — has to be patched by that same handler, not recomputed only at render time, or it goes stale after the first click. Watch for this if more derived-from-pills UI gets added.
- `instanceDisplayName(bucket, pathId, instanceId)` numbers instances by their position in `v6Store[bucket]` filtered to that `pathId` — it's order-dependent, not stored. Don't reorder `v6Store` arrays without expecting displayed "(2)", "(3)" labels to shift.
- "Evaluation and Feedback" is the one project with `project.evalForms` (array) instead of `project.evalFormUrl` (string) — `renderCardProject` branches on which key is present. Any other multi-form project would need the same treatment.
- Eval form URL matching (`build-catalog.js`, not checked in — was a one-off scratchpad script) used stemmed word-bag containment, not exact match; re-run it if `pathways.json` or the eval-forms repo contents change, don't hand-edit individual `evalFormUrl` values.

## Danger Zones (pre-Phase 6 — `PATHS_DATA`/`renderPath`, now dormant/unused)
- `renderLevel` takes `(path, levelIdx, level)` — path object needed for type/defaultStatus
- Active/Completed section membership was explicit (`ACTIVE_PATH_IDS` / `COMPLETED_PATH_IDS`), not inferred
- `initDefaults()` must run AFTER `loadStore()` and BEFORE any render call
- Elective key format is slug-based: `{pathId}.L{n}.elec.{slug}` — numeric elec keys are stale
- CSS `:has()` selector used for elec-badge coloring — requires Chrome 105+, Firefox 121+, Safari 15.4+
