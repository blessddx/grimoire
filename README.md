# grimoire

Private cross-project toolkit — standards and tools for AI-assisted development.

## Quick Start

1. Copy the template to your project:
   ```bash
   cp ~/grimoire/CLAUDE_TEMPLATE.md ~/your-project/CLAUDE.md
   ```

2. Fill in the project-specific section (Zone 3)

3. Install tools (optional):
   ```bash
   # stress-test skill for Claude Code
   mkdir -p ~/.claude/commands
   ln -sf ~/grimoire/tools/stress-test/SKILL.md ~/.claude/commands/stress-test.md
   ```

## Structure

```
grimoire/
├── CLAUDE.md              # Tells agents what grimoire is
├── CLAUDE_TEMPLATE.md     # Template for any project's CLAUDE.md
├── README.md              # This file
├── sgconfig.yml           # ast-grep root config
├── standards/             # Agent directives and conventions
│   ├── README.md          # Index of all standards
│   ├── git-safety.md      # Git, filesystem, code safety (inline)
│   ├── curl-wget-security.md  # Network request wrappers (inline)
│   ├── nix.md             # Nix dev environments (inline)
│   ├── credential-protection.md # Credential deny rules (inline)
│   ├── changelog-format.md    # Changelog structure (reference)
│   ├── jj-workflow.md     # Jujutsu VCS guardrails (reference)
│   ├── design-engineer.md # UI/UX design prompt (reference)
│   ├── security-scan.md   # grep + codex security analysis (reference)
│   ├── ast-grep-hook.md   # Pre-commit lint hook (reference)
│   └── push-protection.md # Git push hook (reference, placeholder)
└── tools/
    ├── ast-grep/          # 24 code quality rules across 4 languages
    └── stress-test/       # Adversarial plan stress-testing skill
```

## Standards

Agent directives split into two modes:

- **Inline** — always active, included in every project's CLAUDE.md (git safety, curl/wget security, nix, credential protection)
- **Reference** — consulted when relevant (jj workflow, design engineering, changelog format, security scan, ast-grep hook, push protection)

See [standards/README.md](standards/README.md) for the full index.

## Tools

### ast-grep

24 code quality rules for Go, Python, Rust, and TypeScript. Catches security issues (hardcoded secrets, SQL injection, XSS), reliability bugs (swallowed errors, empty critical sections), and bad practices (print statements, `as any`).

```bash
# Scan a project with all rules
sg scan -c ~/grimoire/sgconfig.yml /path/to/project

# Run tests
cd ~/grimoire && sg test
```

### stress-test

Adversarially stress-test a technical plan before you build it. Decomposes claims, verifies against real docs via parallel sub-agents, runs POC code, and updates the plan.

```bash
# Install (symlink — stays up to date)
ln -sf ~/grimoire/tools/stress-test/SKILL.md ~/.claude/commands/stress-test.md

# Invoke in Claude Code
/stress-test
```

## Philosophy

- **Reference in-place** — projects point to grimoire via paths and symlinks, never copy files
- **Inline vs reference** — critical safety rules are inlined; domain-specific guides are linked
- **Tools are self-contained** — each tool works independently with no shared dependencies
