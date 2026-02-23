# CLAUDE.md — {project-name}

{One-line description of this project.}

---

## Zone 1: Always-On Rules

### Git Safety

- **NEVER push to master/main without double confirmation** - Ask permission TWICE before pushing to master/main.
- **NEVER force push** - `git push --force` / `-f` is forbidden.
- **NEVER skip hooks** - Do not use `--no-verify`.
- **NEVER use `git reset --hard`** - Use `git stash` or ask the user.
- **NEVER use `git clean -fd`** - Ask the user first.
- **NEVER amend pushed commits** - Create a new commit instead.
- **ALWAYS work on feature branches** - Don't commit directly to master/main unless instructed.

Full rules: `~/grimoire/standards/git-safety.md`

### File System Safety

- **NEVER use `rm -rf`** - Use `trash` instead. A hook blocks `rm -rf` automatically.
- **NEVER delete migration files** - Migrations are append-only.
- **NEVER modify .env files without asking** - They may contain secrets.
- **NEVER commit secrets** - Check for secrets before staging.

### Destructive Operations

Ask for explicit permission before: dropping databases, `sudo`, modifying system files, `git branch -D`, reverting multiple commits.

### Code Safety

- **NEVER introduce known vulnerabilities** - No OWASP Top 10.
- **NEVER disable security features** - No disabling CORS, auth, rate limiting without approval.
- **ALWAYS validate at boundaries** - User input, API responses, external data.

### Changelog

- **ALWAYS update CHANGELOG.md** after commit-worthy changes
- **Format guide:** `~/grimoire/standards/changelog-format.md`

### curl/wget Security

System-wide wrappers block homograph/IDN attacks. If a request is BLOCKED: stop, alert the user, wait for instructions. Never bypass.

Full docs: `~/grimoire/standards/curl-wget-security.md`

### Nix

- **ALWAYS use Nix flakes** for dev environments. Never install project deps globally.
- **Starter template:** `~/grimoire/standards/nix.md`

### Credential Protection

Agent must never read credential stores (`~/.ssh`, `~/.aws`, `~/.kube`, `~/.gnupg`, etc.) or edit shell configs (`~/.bashrc`, `~/.zshrc`). Configure deny rules per `~/grimoire/standards/credential-protection.md`.

---

## Zone 2: Available Tools & References

These are not always-on — use when relevant to the task.

| Resource | When | Location |
|----------|------|----------|
| jj Workflow | Project uses jj | `~/grimoire/standards/jj-workflow.md` |
| Design Engineer | UI work, design reviews | `~/grimoire/standards/design-engineer.md` |
| Security Scan | Security reviews, audits | `~/grimoire/standards/security-scan.md` |
| ast-grep | Code quality scans | `sg scan -c ~/grimoire/sgconfig.yml` |
| ast-grep Hook | Pre-commit lint setup | `~/grimoire/standards/ast-grep-hook.md` |
| stress-test | Plan verification | `/stress-test` or `~/grimoire/tools/stress-test/SKILL.md` |
| Push Protection | Git hook setup (placeholder) | `~/grimoire/standards/push-protection.md` |

---

## Zone 3: Project-Specific

{Fill in project-specific context below. What is this project? What should Claude know about the codebase, architecture, conventions, or quirks?}
