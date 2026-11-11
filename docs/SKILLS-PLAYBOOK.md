# Skills Playbook

The handbook for every skill installed in this repo — what it does, when to reach for it, and how they chain together. Last audited: 2026-07-10 (all skills current with upstream).

## How skills trigger

- **You type `/skill-name`** — the main way. Deliberate invocation is the habit to build.
- **Claude auto-invokes** a skill when its description matches the situation (e.g. you say "debug this" → `diagnosing-bugs` loads). The 2026-07 upstream update improved these trigger descriptions.
- **Skills chain**: `/implement` drives `/tdd` and finishes with `/code-review` on its own.

## The core loop (feature work)

```
/grill-with-docs  →  /to-spec  →  /to-tickets  →  /implement   (per ticket, fresh session)
   sharpen idea       write PRD     slice + order    tdd → review → commit
```

## Skill-by-skill

### Planning
| Skill | Reach for it when | Output |
|---|---|---|
| `/grill-with-docs` | You have a fuzzy idea or plan that needs hard questions | ADRs + glossary |
| `/to-spec` | Right after grilling, same conversation | A PRD synthesized from the discussion — no new questions |
| `/to-tickets` | You have a spec to break down | Tracer-bullet vertical-slice tickets with "blocked by" edges, in `.scratch/` |

### Building
| Skill | Reach for it when | Notes |
|---|---|---|
| `/implement` | Starting a ticket | Chains `/tdd` → typecheck → full suite → `/code-review` → commit |
| `/tdd` | Coding test-first without a ticket | Red → green → refactor, one vertical slice at a time; never all-tests-then-all-code |

### Reviewing
| Skill | Reach for it when | Notes |
|---|---|---|
| `/scrutinize` | Big change, design doc, or you want a second opinion | Asks "should this exist at all?", then traces code paths end-to-end |
| `/code-review` (built-in) | Routine diff check | `implement` already runs this for you |

**Lane split:** routine diff → `code-review`. High-stakes change or a *plan* → `scrutinize`.

### Debugging
| Skill | Reach for it when | Notes |
|---|---|---|
| `/diagnosing-bugs` | A bug survived 2–3 naive fix attempts, or perf regressed | Phase 1 (build a red-capable feedback loop) is the whole skill — no repro command, no hypothesizing |
| `/post-mortem` | Right after a hard bug is fixed and validated | Refuses to run without all four: repro, root cause, fix, validation |

### Learning & continuity
| Skill | Reach for it when | Notes |
|---|---|---|
| `/teach` | Starting or continuing a learning topic | Stateful — uses `MISSION.md`, `lessons/`, `learning-records/`, `RESOURCES.md` in this workspace |
| `/handoff` | Ending a session mid-task | Portable doc a fresh session can resume from |
| `MISSION-FORMAT`, `RESOURCES-FORMAT`, `LEARNING-RECORD-FORMAT` | Never invoked directly | Dependencies of `teach` — don't delete |

## Built-ins you already have (don't install duplicates)

`/code-review`, `/verify` (exercise a change end-to-end), `/simplify` (quality cleanup), `/review` (GitHub PRs), `/security-review`.

## Decision cheatsheet

- New feature idea → `/grill-with-docs`
- Grilling done, want the doc → `/to-spec`
- Spec done, want tasks → `/to-tickets`
- Picking up a ticket → `/implement`
- "Is this design/PR sound?" → `/scrutinize`
- Stuck on a bug → `/diagnosing-bugs`
- Bug fixed, want the record → `/post-mortem`
- Want to learn something → `/teach`
- Stopping mid-task → `/handoff`

## Maintenance

- `skills-lock.json` pins each skill to its GitHub source + SHA-256 of the upstream `SKILL.md`. Local files in `.claude/commands/` are the upstream file with frontmatter stripped and the description as the first line.
- **Drift check** (run occasionally): re-download each `source`/`skillPath` from the lockfile, hash it, compare to `computedHash`. Mismatch = upstream updated → review the diff, then refresh file + hash.
- Removed as redundant (2026-07-10): `karpathy-guidelines` — its rules are now default agent behavior; recoverable from `multica-ai/andrej-karpathy-skills`.
- Deliberately not installed: `wayfinder` (multi-week multi-person planning — overkill for now), `grilling`/`domain-modeling`/`prototype` (covered by `grill-with-docs` at this scale).
