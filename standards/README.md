# Standards

Agent directives and conventions for use across projects. Each standard is either **inline** (always active) or **reference** (consulted when relevant).

## Inline vs Reference

- **Inline** standards contain rules that should be included directly in a project's `CLAUDE.md`. They apply to every task.
- **Reference** standards are consulted on-demand when the task matches their scope. They're linked from `CLAUDE.md` but not pasted in.

## Index

| Standard | File | Mode | When to use |
|----------|------|------|-------------|
| Git & System Safety | `git-safety.md` | Inline | Always |
| curl/wget Security | `curl-wget-security.md` | Inline | Always |
| Nix Environments | `nix.md` | Inline | Always |
| Credential Protection | `credential-protection.md` | Inline | Always |
| Changelog Format | `changelog-format.md` | Reference | Creating/updating changelogs |
| jj Workflow | `jj-workflow.md` | Reference | Project uses jj (Jujutsu VCS) |
| Design Engineer | `design-engineer.md` | Reference | UI work, design reviews |
| Security Scan | `security-scan.md` | Reference | Security reviews, pre-deploy audits |
| ast-grep Hook | `ast-grep-hook.md` | Reference | Setting up pre-commit lint checks |
| Push Protection | `push-protection.md` | Reference | Setting up git hooks (placeholder) |
