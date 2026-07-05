# ADR-0003: C:\project-cutter is the single source of truth; the Downloads folder is retired

## Status
Accepted (2026-07-05)

## Context
Project planning files lived in `C:\Users\Piraw\Downloads\Project Cutter 🇨🇦` with duplicate copies (trailing-`-` re-downloads, hash-verified identical) and superseded versions (EDA diagram v1, SDLC spreadsheets v3/v5). Two homes for project truth means the plans drift apart — the Downloads Learning_Plan and the workspace roadmap had already diverged on phase order (see ADR-0001).

## Decision
The latest version of each founding document was copied into `docs/planning/` (copies hash-verified before deleting originals): `VISION.md`, `Learning_Plan_2026-06.md` (frozen historical plan), `Cutter_EDA_v2.drawio.xml`, `Project_Cutter_SDLC_Master_v6.xlsx`. All duplicates, superseded versions, and moved originals were deleted from Downloads. Only the unrelated English-study PDF remains there. Living documents (`MISSION.md`, `reference/roadmap.html`, `NOTES.md`) are updated in the workspace only.

## Consequences
- Easier: one place to look; planning docs are now under git version control, so "v6-" style manual versioning is never needed again.
- Harder: nothing meaningful — the deleted files were exact duplicates or superseded versions.
