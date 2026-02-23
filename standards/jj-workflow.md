# Claude-Safe jj (Jujutsu) Workflow

> **Mode:** Reference | **When:** Project uses jj

VCS guardrails for AI coding agents (Claude Code) using Jujutsu.

---

## Why jj?

Jujutsu (jj) is a Git-compatible VCS that solves specific problems with AI-assisted development:

| Problem with git | How jj fixes it |
|------------------|-----------------|
| Staging area confusion (`git add`, `git stash`) | Working copy is always a commit. Every save is snapshotted. |
| Worktrees needed for parallel Claude sessions | `jj new` / `jj edit` to switch between changes. No worktrees needed. |
| `git reset --hard` destroys work | `jj undo` reverses any operation. Full operation log. |
| Commit hashes change on rebase | Change IDs are stable across rebases. |
| Conflicts block progress | jj allows committing conflicted files, resolve later. |

**Mode:** Colocated (`.jj` alongside `.git`). GitHub still uses git. Both work.

---

## Philosophy

### Review Philosophy

**Preserve the journey, not just the destination.**

- **Keep intermediate commits** - "try approach A" then "revert, try B" is valuable context for reviewers. Do not squash these away.
- **Keep debug commits** - "add tracing to match loop" is a legitimate commit. Don't delete it.
- **Slightly messy is correct** - 10 honest small changes > 1 perfectly curated change. The log is a lab notebook, not a press release.
- **Never auto-clean history** - Do not squash, reorder, or rebase to "tidy up" unless the user explicitly asks.
- **Describe, don't polish** - Use `jj describe` to add clear messages. Don't rewrite the change itself for aesthetics.

**When to clean up (user-initiated only):**
- User says "squash these" -> `jj squash`
- User says "rebase onto main" -> show log, confirm target, rebase
- User says "abandon this" -> `jj abandon`

**Claude's job:** Make changes, describe them well, show diffs. User decides what ships.

### GitHub Philosophy

**Approval-gated, never autonomous.** Claude can push and create PRs, but only after the user explicitly approves each action. Claude never auto-merges, never interacts with merge queues, never force pushes.

---

## Guardrails

### Hard Rules (NEVER)

| Rule | Why |
|------|-----|
| **NEVER `jj git push` without user approval** | Must show `jj log` + `jj diff`, state what's being pushed and to which branch, ask "OK to push?", wait for explicit yes. |
| **NEVER `git push` without user approval** | Same rule, git path. |
| **NEVER force push** | `--force` / `-f` is forbidden. If a push fails, ask user. |
| **NEVER `jj abandon`** | Destroying changes requires explicit user permission. |
| **NEVER `jj squash` / `jj diffedit`** | History rewriting requires user permission. |
| **NEVER `jj rebase` without showing `jj log` first** | Must show log, identify source+destination by change ID, get user confirmation. |
| **NEVER reference commits by hash** | Always use **change IDs** (short letter IDs like `kpqrstvw`). Change IDs survive rebases; hashes don't. |
| **NEVER `jj git import` / `jj git export` manually** | Colocated mode handles this. |

### Required Practices (ALWAYS)

| Rule | Why |
|------|-----|
| **ALWAYS `jj log --limit 10` before mutating** | Paste output. Identify target by change ID + description. Confirm correct target. |
| **ALWAYS `jj describe` with meaningful message** | Every change must have a description. |
| **ALWAYS `jj new` to start new work** | Don't edit in-place on existing described changes. |
| **ALWAYS `jj status` after making edits** | Show user what files changed. |
| **ALWAYS `jj diff` before asking user to review** | Show what changed. |

### Context Protocol

Before any jj mutating operation:

1. Run `jj log --limit 10`
2. Copy relevant output into response
3. State which change ID you're targeting and why
4. Execute the operation
5. Run `jj log --limit 10` again to confirm result

---

## Command Reference

### Safe Commands (no permission needed)

| Command | Purpose |
|---------|---------|
| `jj log` | View change graph |
| `jj st` | Working copy status |
| `jj diff` | Current working copy diff |
| `jj diff -r <change>` | Specific change diff |
| `jj show <change>` | Change details |
| `jj new` | New change on top of current |
| `jj new <change>` | New change on top of specific change |
| `jj describe -m "..."` | Set/update description |
| `jj branch list` | List branches |
| `jj branch create <name>` | Create local branch |
| `jj branch set <name> -r <chg>` | Point branch at change |
| `jj op log` | Operation history |

### Requires User Permission (ask first, show context, wait for "yes")

| Command | Reason |
|---------|--------|
| `jj git push --branch <name>` | Push to remote - show log+diff first |
| `gh pr create` | Create PR - show title+body first |
| `jj abandon <change>` | Destroys a change |
| `jj squash` | Merges into parent |
| `jj rebase -r <src> -d <dst>` | Moves changes - show log first |
| `jj restore` | Discards working copy |
| `jj undo` | Undoes last operation |

---

## Workflow Patterns

### Starting new work
```bash
jj log --limit 5          # See where we are
jj new                    # New change on top of current
jj describe -m "feat: add user profile endpoint"
# ... make edits ...
jj st                     # Show what changed
jj diff                   # Show the diff
```

### Working on multiple things
```bash
jj log --limit 10         # See all changes
jj new <change-id>        # Branch off a specific change
jj describe -m "fix: correct filter query"
# ... make edits ...
```

### Checking state
```bash
jj log --limit 10         # Overview
jj diff                   # Current working copy changes
jj diff -r <change-id>    # Specific change's diff
jj show <change-id>       # Full change details
```

### Preparing for user to push
```bash
jj log --limit 10                        # Show state
jj branch set feature-x -r <change>     # Point branch at change
# Tell user: "Ready to push. Run: jj git push --branch feature-x"
```

### Push workflow (approval-gated)
```
Claude: "Here's what's ready to push:"
  [pastes jj log output]
  [pastes jj diff output]
  "Pushing branch `feature-x` to origin. This includes: <summary>"
  "OK to push?"

User: "yes"

Claude: jj git push --branch feature-x
```

### PR workflow (approval-gated)
```
Claude: "Here's the PR I'd like to create:"
  Title: "feat: add user profile endpoint"
  Body: [summary]
  "OK to create this PR?"

User: "yes"

Claude: gh pr create --title "..." --body "..."
```

---

## Setup (per repo)

### 1. Add jj to dev environment

In `flake.nix` (Nix) or install globally:
```nix
# flake.nix buildInputs:
jj
```

### 2. Initialize colocated repo

```bash
jj git init --colocate    # .jj alongside existing .git
```

### 3. Add to .gitignore

```
.jj/
```

### 4. Configure user (repo-local)

jj will use your git config. Or set repo-local:
```bash
jj config set --repo user.name "your-name"
jj config set --repo user.email "your@email.com"
```

### 5. Add rules to CLAUDE.md

Copy the VCS rules from the Guardrails section above into the repo's `CLAUDE.md` project-specific section. Add a note that jj rules override the shared git rules.

---

## Key Concepts for Claude

### Change IDs vs Commit Hashes

```
@  kpqrstvw  user@email  2026-02-10  abc123def  (empty)
|  (no description set)
o  mnopqrst  user@email  2026-02-10  456789abc
|  feat: add status field to users
o  zyxwvuts  user@email  2026-02-09  def456789
   initial commit
```

- `kpqrstvw` = **change ID** (stable, use this)
- `abc123def` = commit hash (changes on rebase, don't use)
- `@` = working copy (current change)

### Working Copy is a Commit

In jj, the working copy is always part of a change. There's no "unstaged" or "untracked" state. When you save a file, it's automatically part of the current change. Use `jj new` to start a fresh change when you want to separate work.

### Undo Anything

```bash
jj op log          # See all operations
jj undo            # Undo last operation
jj op restore <n>  # Restore to any previous state
```

This makes jj much safer than git for AI agents - any mistake is reversible.

---

## Adopting in a Project

When evaluating whether to adopt jj in a repo, consider:

| Repo Type | jj Benefit | Recommendation |
|-----------|-----------|----------------|
| Active API/app development | High | Adopt |
| Services with moderate activity | Medium | Adopt if active |
| Documentation repos | Low | Keep git |
| Data repos (infrequent changes) | Low | Keep git |
| LFS repos (large binary files) | Needs testing | Test first |

To adopt: follow Setup steps above, then reference this file from the project's `CLAUDE.md`.
