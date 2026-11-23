# proof-before-done

An agent skill that stops your AI coding agent from saying "done" until it has run the command that proves it, and shows you the output.

Works with Claude Code and any agent harness that loads `SKILL.md` skills.

## Why

The most common way agents waste your time isn't bad code. It's a confident "Fixed! All tests pass" when the tests were never run, the build breaks, or a subagent's "success" changed nothing. This skill turns every completion claim into a quick check:

- every claim needs fresh proof from this turn
- the proof has to match the claim (lint passing doesn't prove the build works)
- the agent reports what it ran and what came back, including gaps
- if it can't verify, it says so instead of guessing

## How it works

```
  about to say "done" / "fixed" / "passing" / commit / PR
          │
          ▼
  1. Name the claim
          │
          ▼
  2. Pick the proof ──── claim → proof table
          │
          ▼
  3. Run it now, in full
          │
          ▼
  4. Read the output ─── exit code, failures, warnings
          │
          ▼
  5. Report ──────────── Status / Proof / Result / Gaps
```

| Claim | Proof that counts | Doesn't count |
|---|---|---|
| Tests pass | Full suite, 0 failures, this turn | Earlier run, "should pass" |
| Build works | Build exits 0 | Lint passing |
| Bug fixed | Original repro now behaves | Code changed |
| Subagent did it | You read the diff | Its "success" message |
| Deployed | Hit the URL, see new behavior | Deploy command finished |

## Install

**Claude Code (personal, all projects):**

```bash
git clone https://github.com/RK905/proof-before-done /tmp/pbd
cp -r /tmp/pbd/skills/proof-before-done ~/.claude/skills/
```

**One project only:** copy `skills/proof-before-done` into `<project>/.claude/skills/`.

**Other harnesses:** point your agent at `skills/proof-before-done/SKILL.md` (Codex, Cursor, Gemini CLI, etc. can load it as an instruction file or rule).

## Usage

The skill triggers on its own whenever the agent is about to report completion, commit, push, or open a PR. You can also call it directly:

```
/proof-before-done verify the checkout fix before I merge
```

| Input | What it is | Example |
|---|---|---|
| `claim` | What should be true | `checkout no longer double-charges` |
| `proof` | Command or check, if not obvious | `pnpm test && pnpm build` |
| `spec` | Requirements to check against (optional) | `docs/plan.md` |

**Example output:**

```
Status: partly done
Proof:  pnpm test && pnpm build
Result: 211 passed, 0 failed; build exit 0
Gaps:   payment webhook not exercised locally (needs Stripe CLI)
```

## Tips

- Pair it with a test-first skill (e.g. [fail-first](https://github.com/RK905/fail-first)) so regression tests are proven to fail without the fix.
- Tell the agent your project's real check commands once (in `CLAUDE.md` or similar) so it picks the right proof every time.
- "Not verified" is a fine answer. It's much cheaper than a false "done".

## Credits

Based on the `verification-before-completion` skill from [obra/superpowers](https://github.com/obra/superpowers) by Jesse Vincent, MIT licensed. See [NOTICE](NOTICE) for what changed.

## License

MIT. See [LICENSE](LICENSE).
