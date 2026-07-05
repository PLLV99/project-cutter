# ADR-0002: The aspirational README becomes VISION.md; the real README grows honestly

## Status
Accepted (2026-07-05)

## Context
The June 2026 README was written as if the project were finished: "Status: MVP functional", a "What I Learned" section, test commands for code that does not exist. The user's whole mission is to be able to defend every claim in a live interview. A published README describing unbuilt features is a fabricated portfolio — if a recruiter asks about the Kafka outbox relay and the candidate has never run Kafka, the interview is over.

## Decision
The document is renamed `docs/planning/VISION.md` with a warning banner: it is the target state, not the current state. The repo README stays minimal and only gains a claim when that claim is true and the user can explain it. The vision doc is still valuable — it is the destination the roadmap drives toward.

## Consequences
- Easier: zero risk of being caught overstating; the README doubles as an honest progress tracker; writing each new README section becomes interview practice.
- Harder: the repo looks modest for a while. That is acceptable — a small true README beats a large false one.
