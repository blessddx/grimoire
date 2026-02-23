# Security Scan

> **Mode:** Reference | **When:** Security reviews, pre-deploy audits, suspicious code

Two-phase security analysis: grep finds suspects, Codex CLI analyzes them. Combines structural pattern matching (fast, zero false negatives on pattern) with LLM reasoning (understands context, rates severity).

---

## Prerequisites

```bash
npm install -g @openai/codex
```

Codex CLI must be authenticated (`codex login` or `CODEX_API_KEY` env var).

---

## Quick Scan

Run from the project root:

```bash
{
  echo "You are a security auditor. Analyze the following code patterns found by grep."
  echo "For each finding, determine if it is a real vulnerability or a false positive."
  echo "Rate severity: Critical / High / Medium / Low / False Positive."
  echo "Output a markdown table with: File, Line, Pattern, Severity, Explanation."
  echo ""
  echo "=== Dangerous Function Calls ==="
  rg --no-heading -n "eval\(|exec\(|system\(|popen\(|subprocess" --type py . 2>/dev/null || echo "(none)"
  rg --no-heading -n "eval\(|new Function\(" --type js --type ts . 2>/dev/null || echo "(none)"
  echo ""
  echo "=== SQL Injection ==="
  rg --no-heading -n -C2 'execute\(.*%|execute\(.*\.format|execute\(.*\+' --type py . 2>/dev/null || echo "(none)"
  rg --no-heading -n -C2 'query\(.*\+|query\(.*\$\{' --type js --type ts . 2>/dev/null || echo "(none)"
  echo ""
  echo "=== Hardcoded Secrets ==="
  rg --no-heading -n -i 'password\s*=\s*["\x27]|api_key\s*=\s*["\x27]|secret\s*=\s*["\x27]|token\s*=\s*["\x27]' . 2>/dev/null || echo "(none)"
  echo ""
  echo "=== XSS / HTML Injection ==="
  rg --no-heading -n "innerHTML|dangerouslySetInnerHTML|document\.write|v-html" --type js --type ts . 2>/dev/null || echo "(none)"
  echo ""
  echo "=== Path Traversal ==="
  rg --no-heading -n 'open\(.*request|os\.path\.join\(.*input|\.\./' --type py . 2>/dev/null || echo "(none)"
  echo ""
  echo "=== Unsafe Deserialization ==="
  rg --no-heading -n 'pickle\.load|yaml\.load\(|marshal\.load' --type py . 2>/dev/null || echo "(none)"
  rg --no-heading -n 'JSON\.parse\(.*body|deserialize' --type js --type ts . 2>/dev/null || echo "(none)"
} | codex exec --sandbox read-only --ask-for-approval never --ephemeral -o security-report.md -
```

Report lands in `security-report.md`.

---

## Grep Pattern Library

### Python

| Category | Pattern |
|----------|---------|
| Command injection | `eval\(\|exec\(\|system\(\|popen\(\|subprocess` |
| SQL injection | `execute\(.*%\|execute\(.*\.format\|execute\(.*\+` |
| Secrets | `password\s*=\s*["\x27]\|api_key\s*=\s*["\x27]\|secret\s*=\s*["\x27]` |
| Path traversal | `open\(.*request\|os\.path\.join\(.*input` |
| Deserialization | `pickle\.load\|yaml\.load\(\|marshal\.load` |
| SSRF | `requests\.get\(.*input\|urllib\.request\.urlopen` |

### TypeScript / JavaScript

| Category | Pattern |
|----------|---------|
| XSS | `innerHTML\|dangerouslySetInnerHTML\|document\.write\|v-html` |
| Injection | `eval\(\|new Function\(` |
| SQL injection | `query\(.*\+\|query\(.*\$\{` |
| Secrets | `apiKey:\s*["\x27]\|password:\s*["\x27]\|secret:\s*["\x27]` |
| Prototype pollution | `__proto__\|Object\.assign\(.*req\.body` |

### Go

| Category | Pattern |
|----------|---------|
| SQL injection | `fmt\.Sprintf.*SELECT\|fmt\.Sprintf.*INSERT\|".*\+.*WHERE` |
| Command injection | `exec\.Command\(.*input\|os\.system` |
| Secrets | `password\s*:=\s*"\|apiKey\s*:=\s*"\|secret\s*:=\s*"` |

### Rust

| Category | Pattern |
|----------|---------|
| Unsafe | `unsafe\s*\{` |
| SQL injection | `format!.*SELECT\|format!.*INSERT` |
| Secrets | `let\s+password\s*=\s*"\|let\s+api_key\s*=\s*"` |

---

## Codex Flags Reference

| Flag | Purpose |
|------|---------|
| `--sandbox read-only` | Analysis only, no file writes |
| `--ephemeral` | Don't persist session to disk |
| `--ask-for-approval never` | Non-interactive |
| `-o <file>` | Write final response to file |
| `--json` | Machine-parseable JSON event stream |
| `-` (as prompt) | Read prompt from stdin |
| `--model o4-mini` | Faster/cheaper model for triage |

---

## Integration with ast-grep

For structural analysis (catches things grep misses), also run:

```bash
sg scan -c ~/grimoire/sgconfig.yml .
```

ast-grep catches patterns like `as any`, `.unwrap()`, `except: pass` that grep patterns would miss or over-match on. Use both: grep + codex for contextual analysis, ast-grep for structural lint.

---

## Claude Code Usage

From a Claude Code session, ask the agent to:

```
Run a security scan on this project using the grep patterns from
~/grimoire/standards/security-scan.md, then pipe results to codex for analysis.
```

The agent can read this file, construct the grep pipeline, invoke `codex exec`, and return the report.
