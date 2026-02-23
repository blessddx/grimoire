# CLAUDE.md — grimoire

Grimoire is a private cross-project toolkit of standards and tools for AI-assisted development. It lives at `~/grimoire` and is referenced in-place by other projects — never copied.

## Directory Structure

```
grimoire/
├── CLAUDE.md              # This file
├── CLAUDE_TEMPLATE.md     # Template for any project's CLAUDE.md
├── README.md              # Toolkit overview
├── sgconfig.yml           # ast-grep root config
├── standards/             # Agent directives and conventions
│   ├── README.md          # Index of all standards
│   ├── git-safety.md      # Git, filesystem, and code safety rules
│   ├── curl-wget-security.md  # Network request security wrappers
│   ├── nix.md             # Nix dev environment rules + starter flake
│   ├── credential-protection.md # Credential filesystem deny rules
│   ├── changelog-format.md    # Changelog structure and versioning
│   ├── jj-workflow.md     # Jujutsu VCS guardrails
│   ├── design-engineer.md # UI/UX design engineering prompt
│   ├── security-scan.md   # grep + codex security analysis
│   ├── ast-grep-hook.md   # Pre-commit lint hook setup
│   └── push-protection.md # Git push hook (placeholder)
└── tools/
    ├── ast-grep/          # 24 code quality rules (Go/Python/Rust/TypeScript)
    │   ├── rules/{lang}/
    │   ├── tests/{lang}/
    │   └── utils/
    └── stress-test/       # Adversarial plan stress-testing skill
        ├── SKILL.md
        └── README.md
```

## How to Use Grimoire

**Reference in-place, don't copy files.** Projects point to grimoire via file paths and symlinks. This means updates to grimoire propagate to all projects automatically.

### Standards

Standards are split into two modes:

- **Inline** (git-safety, curl-wget-security, nix, credential-protection) — rules that apply to every task. These get referenced or inlined in a project's CLAUDE.md.
- **Reference** (jj-workflow, design-engineer, changelog-format, security-scan, ast-grep-hook, push-protection) — consulted when the task matches their scope.

See `standards/README.md` for the full index.

### Tools

**ast-grep** — Static analysis rules. Scan any project:
```bash
sg scan -c ~/grimoire/sgconfig.yml /path/to/project
```

**stress-test** — Adversarial plan stress-testing. Install as a Claude Code skill:
```bash
ln -sf ~/grimoire/tools/stress-test/SKILL.md ~/.claude/commands/stress-test.md
```
Then invoke with `/stress-test` in any conversation with a technical plan.

## CLAUDE_TEMPLATE.md

When setting up a new project, copy `CLAUDE_TEMPLATE.md` to the project root as `CLAUDE.md` and fill in the project-specific section. The template references grimoire standards and tools so the project inherits all rules automatically.

## Active Rules

All inline standards apply when working in this repo. Read and follow:
- `standards/git-safety.md` — git, filesystem, and code safety
- `standards/curl-wget-security.md` — network request security
- `standards/nix.md` — Nix dev environments
- `standards/credential-protection.md` — credential filesystem protection

## Working in This Repo

- Standards are Markdown files — edit directly
- ast-grep rules are YAML — run `sg test` after changes to verify
- The `sgconfig.yml` at root configures rule and test directories
- No build step, no dependencies beyond ast-grep for the linting rules
