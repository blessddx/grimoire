# Push Protection (Git Hook)

> **Mode:** Reference | **When:** Setting up git hooks

Pre-push hook that prevents accidental pushes to protected branches (main/master) and enforces review workflows.

---

## Concept

A `pre-push` git hook that:
- Blocks direct pushes to `main`/`master` unless explicitly overridden
- Warns on force pushes
- Integrates with Claude Code's hook system for agent safety

## Status

**TODO** — Hook implementation exists in another repo and needs to be ported here. This file is a placeholder.

## How It Will Work with Claude Code

Claude Code respects git hooks by default (the `git-safety.md` standard forbids `--no-verify`). The pre-push hook adds a second layer of protection: even if an agent attempts to push to main, the hook blocks it at the git level.

When the hook blocks a push, Claude Code will see the non-zero exit code and the hook's error message, and should report this to the user rather than attempting to bypass it.
