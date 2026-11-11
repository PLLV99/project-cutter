# ADR-0004: Trunk-based development on `main`; CI with GitHub Actions, not Jenkins

## Status
Accepted (2026-07-06)

## Context
Before writing code, we had to pick a branching strategy. The candidate was Git Flow (a long-lived `dev` branch for integration, `main` reserved for production releases). Git Flow makes sense when there is a real gap between "code that is merged" and "code that is deployed" — a staging server, batched releases, a team. This project has one developer, no production deployment yet, and the companies the user is targeting (TD, RBC, Amazon) mostly work trunk-based.

Separately, the user is learning Jenkins in parallel (outside this project), which raised the question of which CI tool the project should use when the Docker/CI phase (Phase 6 of `reference/roadmap.html`) arrives.

## Decision
- Trunk-based development: `main` is the only long-lived branch. Each piece of work gets a short-lived branch (`feature/<thing>`), merged to `main` via PR, then deleted. No `dev` branch.
- Production safety will come from CI (tests run on every PR/merge), not from a buffer branch.
- CI tool: GitHub Actions. The repo already lives on GitHub, Actions is free for this use, needs no server to maintain, and is the most common choice in modern shops. Jenkins knowledge still transfers — pipelines, stages, and triggers are the same concepts — but running a Jenkins server for a solo project is pure overhead.

## Consequences
- Easier: one branch to keep green; the user practices the PR → merge-to-main flow used at the target companies; no `dev`↔`main` sync ceremony; CI setup in Phase 6 is a YAML file in the repo, not a server.
- Harder: nothing today. Revisit only if a real staging/production split appears (then a release branch or environment-based deploys can be added — that would be a new ADR).
- The user's Jenkins study stays valuable as transferable CI concepts and as interview material ("I chose Actions over Jenkins for X reasons" is itself a good story).
