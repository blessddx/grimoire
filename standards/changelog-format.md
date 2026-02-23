# Changelog Format Guide

> **Mode:** Reference | **When:** Creating/updating changelogs

How to structure CHANGELOG.md files across all projects.

---

## Versioning Scheme

```
{prefix}-{phase}.{sequence}
```

- **prefix**: Short repo identifier (e.g., `my-api`, `my-app`, `my-svc`, `my-cli`)
- **phase**: Development phase number (major milestone)
- **sequence**: Incremental within the phase

Each repo picks its own prefix. State it at the top of the changelog.

---

## File Structure

A changelog has 4 sections in this order:

### 1. Header

```markdown
# Changelog — {repo-name}

Versioning: `{prefix}-{phase}.{sequence}` — phase = development phase, sequence = incremental within phase.

**{n} commits** | Last updated: {YYYY-MM-DD}

---
```

### 2. At a Glance (summary table)

A table at the top giving a scannable overview of every version. Each row links to its detailed section below.

```markdown
## At a Glance

| Tag | Date | Commit | Added | Changed | Removed | Fixed | Summary |
|-----|------|--------|-------|---------|---------|-------|---------|
| [{prefix}-2.0](#{prefix}-20) | MM-DD | `abc1234` | 3 features | API rewrite | legacy code | auth bug | Short summary of the release |
| [{prefix}-1.0](#{prefix}-10) | MM-DD | `def5678` | initial setup | | | | Initial release |
```

**Rules:**
- Newest version at the top
- Tag column links to the detailed section via anchor (e.g., `[my-api-2.0](#my-api-20)`)
- Added/Changed/Removed/Fixed columns are brief (a few words each), leave empty if N/A
- Summary is one short sentence

### 3. Cross-cutting summary tables (optional)

After the At a Glance table, add optional tables that track patterns across versions:

```markdown
### What was added (timeline)

\```
{prefix}-1.0  Initial structure (feature A, feature B)
{prefix}-2.0  Added feature C, feature D
\```

### What was removed

| Tag | What | Why |
|-----|------|-----|
| [{prefix}-2.0](#{prefix}-20) | `legacy/` directory | Superseded by new system |

### What changed (breaking)

| Tag | What | Before | After |
|-----|------|--------|-------|
| [{prefix}-2.0](#{prefix}-20) | Internal API | REST | gRPC |
```

**Rules:**
- Only include these tables if there's enough history to justify them
- "What was added" uses a plain code block timeline (tag + one-line summary)
- "What was removed" and "What changed (breaking)" use tables with linked tags
- The "breaking" table has Before/After columns to show exactly what changed

### 4. Detailed version sections

Each version gets its own section with full details. Newest first.

```markdown
---

<a id="{prefix}-20"></a>
## {prefix}-2.0 — Short Title
**{YYYY-MM-DD}** · {MAJOR|MINOR|PATCH} · Commits: `abc1234`, `def5678`

One-paragraph summary of what this version covers.

### Added

- **Feature name** — Description of what was added and why
- **Another feature** — Description

### Changed

- **Thing that changed** — What changed and why (`commit-hash` if relevant)

### Removed

- **`directory/`** — Why it was removed

### Fixed

- **Bug description** — What was fixed

---
```

**Rules:**
- Anchor ID format: `<a id="{prefix}-{phase}{sequence}"></a>` (no dots — `20` not `2.0`)
- Header line: `## {tag} — Short Title`
- Metadata line: `**{date}** · {severity} · Commits: {hashes}`
- Severity is `MAJOR`, `MINOR`, or `PATCH`
- Subsections: only include Added/Changed/Removed/Fixed/Archived if they have content
- Each bullet: `- **Bold name** — Description`
- Separate versions with `---`

---

## Example: New Project Starter

```markdown
# Changelog — my-project

Versioning: `mp-{phase}.{sequence}` — phase = development phase, sequence = incremental within phase.

**3 commits** | Last updated: 2026-02-23

---

## At a Glance

| Tag | Date | Commit | Added | Changed | Removed | Fixed | Summary |
|-----|------|--------|-------|---------|---------|-------|---------|
| [mp-1.0](#mp-10) | 02-23 | `abc1234` | project scaffold | | | | Initial project setup |

---

<a id="mp-10"></a>
## mp-1.0 — Initial Setup
**2026-02-23** · MAJOR · Commits: `abc1234`

Initial project scaffold with core structure.

### Added

- **Project structure** — src/, tests/, config directories
- **CI pipeline** — GitHub Actions for lint + test
- **README** — Usage instructions and setup guide
```

---

## Quick Rules

1. **Always update the changelog** after commit-worthy changes
2. **Always update the At a Glance table** when adding or modifying a version
3. **Newest version first** in both the table and the detailed sections
4. **Use the repo's prefix** — don't mix prefixes across repos
5. **Link tags to anchors** — every tag in the summary table should link to its detailed section
6. **Bold names with em-dash descriptions** — `- **Name** — What it does`
7. **Keep summaries scannable** — the At a Glance table should tell the story at a glance
