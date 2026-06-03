# Pathways Dashboard

## Project
A single-file HTML Toastmasters Pathways progress tracker. Members self-report completions; data lives in localStorage.

## Stack
- Single `.html` file — HTML + CSS + JS, no frameworks, no build step
- localStorage for all persistence
- Pattern: follow Bond7010/toastmasters-pathways-app (`pathways-app.html`)

## Related Project
Bond7010/toastmasters-pathways-app contains all 8 Pathways paths with project names, levels, and competencies. Reuse that data structure rather than recreating it.

## Hosting Constraint
Deployed to FTH (Free Toast Host) at 4039.toastmastersclubs.org. FTH imposes a file size limit — verify the limit against the existing app before adding large assets.

## Repo
https://github.com/Bond7010/pathways-dashboard

## Core Features (v1)
1. Display all active paths with level and progress (completed / total projects)
2. Highlight the next project to give per path
3. Show competencies earned per path
4. Mark projects complete (persisted to localStorage)
5. Add / remove paths from tracking

## What "Done" Looks Like
User opens the page, sees their paths at a glance, checks off a completed project, and immediately sees updated progress and next-up project — without touching Base Camp.

## Decisions
- No login, no backend — keeps it deployable as a static file
- Reuse existing path data from toastmasters-pathways-app to avoid duplicating research
- localStorage only — acceptable for personal use; no cross-device sync needed
