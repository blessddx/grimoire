# Git & System Safety

> **Mode:** Inline | **When:** Always

Core safety rules for AI coding agents. These are non-negotiable guardrails that apply to every project.

---

## Git Safety (Critical)

- **NEVER push to master/main without double confirmation** - Before pushing to master/main, you MUST ask the user for permission TWICE. First ask "Can I push to master?", wait for approval, then ask "Please confirm again that I should push to master." Only proceed after receiving explicit approval both times.
- **NEVER force push** - Commands like `git push --force` or `git push -f` are forbidden. If you need to fix a push issue, ask the user for guidance.
- **NEVER skip hooks** - Do not use `--no-verify` flag with git commit or git push. Hooks exist for safety.
- **NEVER use `git reset --hard`** - This destroys uncommitted work. Use `git stash` or ask the user first.
- **NEVER use `git clean -fd`** - This permanently deletes untracked files. Ask the user first.
- **NEVER amend pushed commits** - If a commit has been pushed to remote, do not use `--amend`. Create a new commit instead.
- **ALWAYS work on feature branches** - Do not commit directly to master/main unless explicitly instructed. Create a feature branch first.

## File System Safety

- **NEVER use `rm -rf`** - Always use `trash` command instead for safe deletion with recovery option. A hook is configured to block `rm -rf` commands automatically.
- **NEVER delete migration files** - Database migrations in any `migrations/` directory are append-only. Never delete or modify existing migrations.
- **NEVER modify .env files without asking** - Environment files may contain secrets. Always ask before modifying `.env`, `.env.local`, `.env.production`, etc.
- **NEVER commit secrets** - Do not commit API keys, passwords, tokens, or credentials. Check for secrets before staging files.

## Destructive Operations (Require Explicit Permission)

Before performing any of these operations, you MUST ask the user for explicit permission:

- Dropping database tables or databases
- Running commands with `sudo`
- Modifying system files outside the project
- Force-deleting branches (`git branch -D`)
- Reverting multiple commits

## Code Safety

- **NEVER introduce known vulnerabilities** - No SQL injection, XSS, command injection, or other OWASP Top 10 vulnerabilities.
- **NEVER disable security features** - Do not disable CORS, authentication, rate limiting, or input validation without explicit approval.
- **ALWAYS validate at boundaries** - Validate user input, API responses, and external data.
