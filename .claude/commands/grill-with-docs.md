Run a relentless interview to sharpen a plan or design. Question every assumption. Create ADRs and a glossary as output.

## What this does

Takes a plan, design doc, or proposed architecture and grills it with hard questions — the kind a senior engineer or tech lead would ask in a design review. The goal is to surface hidden assumptions, weak spots, and missing decisions *before* you start building.

Output artifacts:
- **ADR (Architecture Decision Record)** for each significant decision uncovered
- **Glossary** of domain terms defined during the session

## How to run

1. Share the plan, design, or proposal you want grilled.
2. The session runs as a structured interview — answer each question honestly. "I don't know" is a valid answer and more useful than a guess.
3. After the interview, ADRs and glossary entries are drafted from your answers.

## Question categories (all will be covered)

**Why this, not that?**
- What alternatives did you consider?
- Why did you reject them?
- What would have to be true for the alternative to be better?

**What breaks it?**
- What is the failure mode under load?
- What happens if service X goes down?
- What is the worst-case data input?
- Where is the race condition hiding?

**What are you assuming?**
- What must be true in the environment for this to work?
- What user behavior are you assuming?
- What team capability are you assuming?

**What does it cost?**
- Operational cost at scale?
- Maintenance burden in 6 months?
- Onboarding cost for a new engineer?

**Where is the seam?**
- Where does this touch existing systems?
- What contract does it expose to callers?
- What happens when that contract needs to change?

**Is it testable?**
- How do you know it works?
- What does a failing test look like?
- What can't be tested and why?

## ADR format (one per significant decision)

```
# ADR-NNN: [Decision title]

## Status
Accepted / Proposed / Deprecated

## Context
What situation forced this decision?

## Decision
What was decided?

## Consequences
What becomes easier? What becomes harder?
```

## Rules

- No rubber-stamping. Every section of the plan gets at least one hard question.
- "We'll figure it out later" is a red flag — probe what "later" means and what the risk is until then.
- Surface the assumption explicitly before accepting any answer.
- If an answer reveals a new question, ask it immediately — don't save it for the end.
