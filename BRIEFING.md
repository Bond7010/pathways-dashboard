# Pathways Dashboard — Project Brief

## Purpose
Personal Toastmasters Pathways tracker. At-a-glance: path + level, projects completed/remaining, next project, competencies earned.

## Stack
Single `.html` file · localStorage · no login · no backend
Hosted: GitHub Pages — github.com/Bond7010/pathways-dashboard (no file size constraint)
Data: reuse all 8 paths from Bond7010/toastmasters-pathways-app

## Current State
Canonical: pathways-dashboard.html on GitHub Pages. FTH embedding abandoned.
Phase 6 complete. Three-section layout, Pick Your Path, horizontal cards, eval forms, multi-instance, localStorage, polish done.
Post-Phase-6 fix: removed the global header Enhancements button and member ID; each path card now has its own independent Enhancements toggle instead.
Post-Phase-6 fix: card header controls regrouped left (no elements pushed right); NEW badge now only shows on active-section cards, never completed. Standalone Achievements section removed entirely (HTML, CSS, and print-stylesheet references).
Phase 6 complete + Option B identity: member ID namespacing, first-visit prompt, Export/Import. Ready for member distribution.

## Active Task
None. See HANDOFF.md for details and next-step ideas.

## Key Files
- `pathways-dashboard.html` — the app
- `BRIEFING.md` — session briefing (this file)
- `CLAUDE.md` — architecture reference
