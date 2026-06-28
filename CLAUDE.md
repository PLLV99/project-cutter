# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A Claude Code agent skills repository. Skills are reusable instruction sets that Claude loads on-demand via `/skill-name` commands. `skills-lock.json` is the manifest — it pins each skill to a GitHub source and content hash, similar to a package lockfile.

## Installed skills

| Skill | Trigger | Purpose |
|---|---|---|
| `scrutinize` | `/scrutinize` | Outsider-perspective review of a plan, PR, or code change — questions intent first, then traces code paths end-to-end |
| `post-mortem` | `/post-mortem` | Canonical engineering record of a fixed bug (root cause, fix, validation). Requires all four inputs: repro, root cause, fix, validation |
| `diagnosing-bugs` | `/diagnosing-bugs` | Structured 6-phase debug loop. Phase 1 (build a feedback loop) is the core; never skip to hypothesising without a red-capable command |
| `tdd` | `/tdd` | Red→green→refactor, vertical slice per behavior. Forbids writing all tests before all implementation ("horizontal slicing") |
| `karpathy-guidelines` | `/karpathy-guidelines` | Guards against common LLM coding pitfalls: overcomplication, non-surgical edits, weak success criteria |
| `grill-with-docs` | `/grill-with-docs` | Relentless interview to sharpen a design, producing ADRs and a glossary |

## Infrastructure

`Dockerfile` + `compose.yaml` define the container environment. `.env` holds environment variables for the compose setup.

## Skills lockfile

`skills-lock.json` records `source` (GitHub `owner/repo`), `skillPath` (path within that repo), and `computedHash` for each skill. When adding or updating skills, update this file to keep the manifest consistent.
