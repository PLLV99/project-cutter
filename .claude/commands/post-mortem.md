Write the canonical engineering record of a fixed bug — root cause, mechanism, fix, validation, and how it slipped through. Engineer-audience, code identifiers welcome. Use after a debug session lands a fix, before closing the ticket.

## When NOT to use

- **Bug not fixed yet, or fix not validated.** A post-mortem of a hypothesis is misleading. Refuse and tell the user what's missing.
- **Customer-visible outage / incident.** Those need a separate incident report (timeline, blast radius, paging history, comms). Flag and confirm before producing one.
- **Trivial fix** (typo, obvious one-liner). The PR description is the record. Don't manufacture ceremony.

## Required inputs — refuse to draft without these

Before writing a single line, confirm all four. If any are missing, list what's missing and stop:

- [ ] **Reliable repro** exists (not "happens sometimes" — a deterministic or high-rate-flake repro the next person can run).
- [ ] **Root cause is known** (the mechanism is identified, not a hypothesis).
- [ ] **Fix is identified** (PR / commit / branch pointer).
- [ ] **Fix is validated** (the original repro now passes; the customer workload / failing test now succeeds).

## Structure

Use these blocks in this order. **Summary, Root cause, Fix, and Validation are mandatory.**

### 1. Summary _(mandatory)_
One paragraph. What broke, in user/workload terms. What fixed it, in one sentence. JIRA key, PR number, owner. A reader who stops here should have the right answer.

### 2. Symptom
What was actually observed. Test output, error message, log line, perf number, customer report. Concrete identifiers — don't paraphrase the failure mode.

### 3. Root cause _(mandatory)_
The actual bug mechanism. **Code identifiers welcome and expected** — function names, file paths, struct fields, branch conditions, commit SHAs of the offending change. Walk the cause chain end-to-end.

### 4. Why it produced the symptom
Link the root cause to the symptom. Often non-obvious — walk the chain so a reader who only knows the symptom can connect it back to the cause without re-deriving it.

### 5. Fix _(mandatory)_
What changed and **why this change addresses the root cause** rather than hiding the symptom. Link to PR / commit. If a previous fix attempt papered over the symptom, name it and explain what was wrong with it.

### 6. How it was found
Short. The debugging path: what repro made it deterministic, what tools cracked it, hypotheses tried and rejected (with one-line reason each), the single experiment that confirmed the cause.

### 7. Why it slipped through
What allowed this bug to reach the branch / release / customer. Pick the real reason: CI gap, latent code, workload gap, incomplete prior fix, review miss. **Blameless** — describe the gap, not the person.

### 8. Validation _(mandatory)_
How we know the fix works. Concrete: test names, workload identifiers, run links, perf numbers before/after. If you only validated one configuration, say so explicitly.

### 9. Action items / follow-ups
Concrete next-steps not in the fix PR itself. Each item: what + owner + tracking artifact. If there are none, write "None — the fix is sufficient."

## Tone

Engineer-to-engineer. Code identifiers are first-class. Mechanism over narrative. Active voice, concrete subjects, short paragraphs. No hedging. Blameless.

## Output flow

1. **Confirm all four required inputs are satisfied.** If any are missing, list them and stop.
2. **Confirm where it goes** (default: JIRA comment on the source ticket).
3. **Produce the draft** as a single chat block.
4. **Sign-off before posting.** Show the exact payload, wait for explicit "post it" / "go ahead" / "yes," then post. Print-only output needs no approval.
5. **Offer the management-talk handoff** if needed — don't do it automatically.

## Rules

- **Refuse to draft without all four required inputs.**
- **Never invent root cause, owner, validation runs, or action items.**
- **Never strip code identifiers** in the engineering record.
- **Blameless.** Describe gaps and bugs, never people.
- **State validation coverage honestly.**
- **Get sign-off before posting to JIRA.**
