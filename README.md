# AAG Shared

The single operational source of truth shared across all AllAboutGroup platforms (TSL, CIL, EO) and every developer / Claude Code session.

## Contents
- **DEV-LOG.md** — running log of things that cost development time, plus the known recurring gotchas on the shared stack. Check it first when tooling fails mysteriously; append when you lose time to something.
- **aag-access-register.md** — record of third-party / external access across all platforms, maintained for operational clarity and M&A diligence.
- **engineering-standards.md** — (add) the consolidated architecture, guardrails and security standards for the shared stack.

## How sessions use this repo
Every project's `CLAUDE.md` should instruct the agent to:
1. **Pull this repo at session start.**
2. **Check DEV-LOG.md** when tooling fails in a way that isn't obvious.
3. **Append to DEV-LOG.md** (`date · project · symptom · fix`) when time is lost to a problem, then push.

This turns otherwise-isolated sessions into ones that share one source of truth.

## Access
All AAG platform repos' agents and the core developers (Juan, George) have read/write access. Jack Denton is owner.
