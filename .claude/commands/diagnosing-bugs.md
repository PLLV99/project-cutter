Diagnosis loop for hard bugs and performance regressions. Skip phases only when explicitly justified.

When exploring the codebase, read `CONTEXT.md` (if it exists) to get a clear mental model of the relevant modules, and check ADRs in the area you're touching.

## Phase 1 — Build a feedback loop

**This is the skill.** Everything else is mechanical. If you have a **tight** pass/fail signal for the bug — one that goes red on _this_ bug — you will find the cause. If you don't have one, no amount of staring at code will save you.

Spend disproportionate effort here. **Be aggressive. Be creative. Refuse to give up.**

### Ways to construct one — try them in roughly this order

1. **Failing test** at whatever seam reaches the bug — unit, integration, e2e.
2. **Curl / HTTP script** against a running dev server.
3. **CLI invocation** with a fixture input, diffing stdout against a known-good snapshot.
4. **Headless browser script** (Playwright / Puppeteer) — drives the UI, asserts on DOM/console/network.
5. **Replay a captured trace.** Save a real network request / payload / event log to disk; replay it through the code path in isolation.
6. **Throwaway harness.** Spin up a minimal subset of the system that exercises the bug code path with a single function call.
7. **Property / fuzz loop.** If the bug is "sometimes wrong output", run 1000 random inputs and look for the failure mode.
8. **Bisection harness.** If the bug appeared between two known states, automate "boot at state X, check, repeat" so you can `git bisect run` it.
9. **Differential loop.** Run the same input through old-version vs new-version and diff outputs.
10. **HITL bash script.** Last resort. If a human must click, drive _them_ with `scripts/hitl-loop.template.sh` so the loop is still structured.

### Tighten the loop

- Can I make it faster? (Cache setup, skip unrelated init, narrow the test scope.)
- Can I make the signal sharper? (Assert on the specific symptom, not "didn't crash".)
- Can I make it more deterministic? (Pin time, seed RNG, isolate filesystem, freeze network.)

### Completion criterion — a tight loop that goes red

Phase 1 is done when you can name **one command** you have **already run at least once** that is:

- [ ] **Red-capable** — drives the actual bug code path and asserts the user's exact symptom
- [ ] **Deterministic** — same verdict every run
- [ ] **Fast** — seconds, not minutes
- [ ] **Agent-runnable** — no human in the loop (or uses `scripts/hitl-loop.template.sh`)

**If you catch yourself reading code to build a theory before this command exists, stop.** No red-capable command, no Phase 2.

## Phase 2 — Reproduce + minimise

Run the loop. Confirm:

- [ ] The loop produces the failure mode the **user** described — not a different failure nearby.
- [ ] The failure is reproducible across multiple runs.
- [ ] You have captured the exact symptom so later phases can verify the fix addresses it.

Then shrink the repro to the **smallest scenario that still goes red**. Cut inputs, callers, config, data, and steps one at a time, re-running after each cut. Done when every remaining element is load-bearing.

## Phase 3 — Hypothesise

Generate **3–5 ranked hypotheses** before testing any. Each must be **falsifiable**:

> "If \<X\> is the cause, then \<changing Y\> will make the bug disappear / \<changing Z\> will make it worse."

Show the ranked list to the user before testing. Don't block on it — proceed if user is AFK.

## Phase 4 — Instrument

Each probe must map to a specific prediction from Phase 3. **Change one variable at a time.**

1. **Debugger / REPL inspection** if the env supports it. One breakpoint beats ten logs.
2. **Targeted logs** at the boundaries that distinguish hypotheses.
3. Never "log everything and grep".

**Tag every debug log** with a unique prefix, e.g. `[DEBUG-a4f2]`. Cleanup becomes a single grep.

**Perf branch:** For performance regressions, establish a baseline measurement first, then bisect. Measure first, fix second.

## Phase 5 — Fix + regression test

Write the regression test **before the fix** — but only if there is a **correct seam** for it. A correct seam exercises the real bug pattern as it occurs at the call site.

If no correct seam exists, note it — the codebase architecture is preventing the bug from being locked down.

If a correct seam exists:
1. Turn the minimised repro into a failing test at that seam.
2. Watch it fail.
3. Apply the fix.
4. Watch it pass.
5. Re-run the Phase 1 loop against the original (un-minimised) scenario.

## Phase 6 — Cleanup + post-mortem

Required before declaring done:

- [ ] Original repro no longer reproduces (re-run the Phase 1 loop)
- [ ] Regression test passes (or absence of seam is documented)
- [ ] All `[DEBUG-...]` instrumentation removed (`grep` the prefix)
- [ ] Throwaway prototypes deleted
- [ ] The hypothesis that turned out correct is stated in the commit / PR message

Then ask: what would have prevented this bug? If the answer involves architectural change, flag it after the fix is in.
