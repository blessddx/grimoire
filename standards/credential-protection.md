# Credential & Filesystem Protection

> **Mode:** Inline | **When:** Always

Deny rules for Claude Code's `settings.json` that prevent agents from reading sensitive credential stores, key material, and wallet data.

---

## Rule

Agents must never read files in credential directories. These deny rules block access at the tool level — the agent cannot even open these paths.

## settings.json Configuration

Add to `~/.claude/settings.json` (user-level) or `.claude/settings.json` (project-level):

```json
{
  "permissions": {
    "deny": [
      "Read(~/.ssh/**)",
      "Read(~/.aws/**)",
      "Read(~/.kube/**)",
      "Read(~/.gnupg/**)",
      "Read(~/.config/gcloud/**)",
      "Read(~/.azure/**)",
      "Read(~/.docker/config.json)",
      "Read(~/.netrc)",
      "Read(~/.npmrc)",
      "Read(~/.pypirc)",
      "Read(~/.gem/credentials)",
      "Read(~/.cargo/credentials*)",
      "Read(**/.env)",
      "Read(**/.env.local)",
      "Read(**/.env.production)",
      "Edit(~/.bashrc)",
      "Edit(~/.zshrc)",
      "Edit(~/.bash_profile)",
      "Edit(~/.zprofile)"
    ]
  }
}
```

## What Each Rule Protects

| Path | Contains |
|------|----------|
| `~/.ssh/**` | SSH private keys, authorized_keys |
| `~/.aws/**` | AWS credentials, config |
| `~/.kube/**` | Kubernetes cluster certs and tokens |
| `~/.gnupg/**` | GPG private keys |
| `~/.config/gcloud/**` | Google Cloud credentials |
| `~/.azure/**` | Azure CLI tokens |
| `~/.docker/config.json` | Docker registry auth |
| `~/.netrc` | Machine credentials (FTP, Git) |
| `~/.npmrc` | npm auth tokens |
| `~/.pypirc` | PyPI upload credentials |
| `~/.gem/credentials` | RubyGems API key |
| `~/.cargo/credentials*` | crates.io API token |
| `**/.env*` | Environment files with secrets |
| `~/.bashrc` etc. | Shell configs — blocks backdoor planting |

## Why Shell Config Edits Are Blocked

A compromised or confused agent could add `export` lines, aliases, or PATH modifications to shell configs that persist beyond the session. Blocking edits to these files prevents:
- Exfiltrating env vars via modified shell startup
- Planting aliases that redirect commands
- Modifying PATH to shadow system binaries

## Extending

Add project-specific deny rules to `.claude/settings.json` in the project root. Common additions:

```json
"Read(secrets/**)",
"Read(**/credentials.json)",
"Read(**/service-account-key.json)"
```
