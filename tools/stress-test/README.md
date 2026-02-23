# stress-test

Adversarially stress-test a technical plan by verifying claims against real docs, running POC code, and updating the plan before you build.

## What it does

Six phases:

1. **Decompose** — Extracts every decision, assumption, dependency, and interface from your plan
2. **Verify** — Launches parallel sub-agents to search docs, repos, and the web for evidence
3. **Triage** — Separates confirmed claims from those needing hands-on testing; drafts minimal POC specs
4. **Approve** — Presents proposed POCs for you to approve, skip, or modify
5. **Test** — Runs approved POCs in parallel in an isolated `.poc-stress-test/` directory
6. **Update** — Walks through findings one by one, applies approved changes inline, cleans up

## Install

All platforms use symlinks so the skill stays in grimoire and updates automatically.

### Claude Code

```bash
mkdir -p ~/.claude/commands
ln -sf ~/grimoire/tools/stress-test/SKILL.md ~/.claude/commands/stress-test.md
```

Then invoke with `/stress-test` in any conversation that has a technical plan.

### Codex

```bash
mkdir -p ~/.codex/skills/stress-test
ln -sf ~/grimoire/tools/stress-test/SKILL.md ~/.codex/skills/stress-test/SKILL.md
```

### Other frameworks

Point your agent to read `~/grimoire/tools/stress-test/SKILL.md` and follow it. The file is self-contained — any agent that reads it gets the full workflow.

## When to use it

- After writing a technical plan, before you start building
- When evaluating a new library, framework, or integration approach
- Before committing to decisions that are expensive to reverse
- Anytime a plan has claims you haven't personally verified
