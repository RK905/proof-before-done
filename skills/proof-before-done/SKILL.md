---
name: proof-before-done
description: Use right before saying work is done, fixed, passing, or ready, and before committing, pushing, opening a PR, or handing off. Requires running the command that proves the claim, reading its output, and reporting the evidence (or the gap) instead of "should work".
---

# Proof Before Done

**Rule:** no claim about the state of the work without fresh evidence from this turn. A claim is anything that says or implies success: "done", "fixed", "tests pass", "ready to merge", "Great, that works!".

## The check (run it every time)

1. **Name the claim** you're about to make.
2. **Pick the proof**: the command or check that would show it's true (table below).
3. **Run it now, in full.** Not an earlier run, not a subset, not someone else's report.
4. **Read the output**: exit code, failure count, warnings, the actual lines.
5. **Report what it showed.** True: state it with the evidence. False or partial: state the real status. Couldn't run it: say so and why.

## Claim → proof

| Claim | Proof that counts | Doesn't count |
|---|---|---|
| Tests pass | Full test command, 0 failures, this turn | Earlier run, one file, "should pass" |
| Build works | Build command exits 0 | Lint or typecheck passing |
| Lint/types clean | That tool's output, 0 errors | Build passing, "looks fine" |
| Bug fixed | Original repro steps now behave correctly | Code changed, assumed fixed |
| Regression test is real | It fails with the fix reverted, passes with it restored | Passes once |
| Subagent/tool did the work | You read the diff or the resulting files | Its "success" message |
| Requirements met | Each requirement checked off against the spec | Tests green |
| Deployed / live | Hit the URL or endpoint, see the new behavior | Deploy command finished |

Scale the proof to the claim: a one-line copy fix needs a look at the rendered text, not the full CI suite. But the proof must match the claim you actually make.

## Stop signs

If you notice any of these, stop and run the check first:

- "should", "probably", "seems to", "I believe it works"
- celebrating before evidence ("Perfect!", "All set!")
- about to commit, push, open a PR, or close a task
- relying on a partial run, a cached result, or another agent's word
- "it's a tiny change, no need" / "I'm confident"

Confidence is not evidence. Rewording the claim to dodge the rule is still a claim.

## How to report

```
Status: <done | partly done | not done | not verified>
Proof:  <command or check you ran>
Result: <key output: "142 passed, 0 failed", exit code, URL response>
Gaps:   <anything not verified, pre-existing failures by name, what's left>
```

Short version for small tasks: "Ran `npm test`: 48/48 pass."

When you can't verify (no test suite, no access, needs a human), say exactly that. "Not verified: no way to run the iOS build here" beats an unproven "done".
