# ast-grep Pre-Commit Hook

> **Mode:** Reference | **When:** Setting up CI or pre-commit checks

Run grimoire's ast-grep rules automatically before commits via Claude Code hooks or git pre-commit hooks.

---

## Claude Code Hook (Recommended)

Add to `.claude/settings.json` in the project root:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "if echo \"$TOOL_INPUT\" | grep -q 'git commit'; then sg scan -c ~/grimoire/sgconfig.yml . 2>&1 | tail -5; fi",
            "blocking": false
          }
        ]
      }
    ]
  }
}
```

This runs `sg scan` whenever the agent attempts a `git commit`, surfacing any lint violations in the hook output. Set `blocking: true` to prevent the commit if violations are found.

## Git Pre-Commit Hook

For non-agent workflows, add a standard git pre-commit hook:

```bash
#!/usr/bin/env bash
# .git/hooks/pre-commit

if ! sg scan -c ~/grimoire/sgconfig.yml .; then
  echo ""
  echo "ast-grep violations found. Fix them before committing."
  exit 1
fi
```

Make it executable:

```bash
chmod +x .git/hooks/pre-commit
```

## Per-Language Scanning

To scan only relevant languages for a project:

```bash
# TypeScript/JavaScript project
sg scan --rule ~/grimoire/tools/ast-grep/rules/typescript/ .

# Go project
sg scan --rule ~/grimoire/tools/ast-grep/rules/go/ .

# Rust project
sg scan --rule ~/grimoire/tools/ast-grep/rules/rust/ .

# Python project
sg scan --rule ~/grimoire/tools/ast-grep/rules/python/ .

# Elixir project
sg scan --rule ~/grimoire/tools/ast-grep/rules/elixir/ .

# Nix configuration
sg scan --rule ~/grimoire/tools/ast-grep/rules/nix/ .
```

## Severity Filtering

ast-grep reports all severities by default. To fail only on errors (skip warnings and hints):

```bash
sg scan -c ~/grimoire/sgconfig.yml . --error 2>&1 | grep -q "error" && exit 1 || exit 0
```
