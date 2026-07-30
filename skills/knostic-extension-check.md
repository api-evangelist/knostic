---
name: extension-check
description: >-
  Use when auditing installed Cursor, VS Code, or VS Code Insiders extensions
  for supply-chain risk against AgentMesh (agentmesh.knostic.ai), reviewing
  GitHub Copilot or other editor extensions, checking extension publishers and
  versions, or running a Knostic extension security review.
---

# Extension Check (Cursor / VS Code → AgentMesh)

Compare every extension installed in **Cursor** or **Visual Studio Code**. The workflow is identical for both editors; only how you list extensions and where they are stored differs.

## Critical rule: search query format

**ALWAYS** search AgentMesh with this exact query style — one query per installed extension, no exceptions:

```
publisher:{publisher} name:{name}
```

- `{publisher}` = segment before the first `.` in the extension id (e.g. `GitHub` from `GitHub.copilot`)
- `{name}` = segment after the first `.` (e.g. `copilot` from `GitHub.copilot`)

Use the **publisher and name from the installed extension id**, preserving marketplace casing in the query (e.g. `publisher:GitHub name:copilot`, not `publisher:github` unless that is what the installed id uses).

**Never** use:
- Raw extension id as the only query (e.g. `q=GitHub.copilot`, `q=golang.go`)
- Publisher-only or name-only queries (e.g. `q=GitHub`, `q=copilot`)
- AgentMesh API endpoints (`/api/extensions`, `/v1/extensions`, or any authenticated API)

## Supported editors

| Editor | CLI command | Extensions manifest |
|--------|-------------|---------------------|
| **Cursor** | `cursor --list-extensions --show-versions` | `~/.cursor/extensions/extensions.json` |
| **VS Code** | `code --list-extensions --show-versions` | `~/.vscode/extensions/extensions.json` |
| **VS Code Insiders** | `code-insiders --list-extensions --show-versions` | `~/.vscode-insiders/extensions/extensions.json` |

On Windows, replace `~` with `%USERPROFILE%` (e.g. `%USERPROFILE%\.vscode\extensions\extensions.json`).

## Prerequisites

- Target editor CLI on PATH (`cursor`, `code`, or `code-insiders`), **or** readable `extensions.json` for that editor
- Network access to `https://agentmesh.knostic.ai`
- A way to load the search page and read results (e.g. **WebFetch** on the search URL, or browser tools). The page is a client-rendered SPA; plain `curl` of the HTML shell will not contain table data.

## Workflow

```
- [ ] Step 0: Detect editor (Cursor vs VS Code vs both)
- [ ] Step 1: List installed extensions
- [ ] Step 2: Build structured search query per extension
- [ ] Step 3: Open search URL and parse results
- [ ] Step 4: Match row and compare version
- [ ] Step 5: Produce report
```

### Step 0: Detect editor

Determine which installation(s) to audit:

1. **User specified** — e.g. “check my VS Code extensions”, “Cursor only”, “VS Code with Copilot”.
2. **Auto-detect** — prefer the editor implied by the workspace or conversation; if unclear, check which CLIs exist:
   ```bash
   command -v cursor && cursor --list-extensions --show-versions | head -1
   command -v code && code --list-extensions --show-versions | head -1
   ```
3. **Both installed** — run separately and label reports (`Cursor` vs `VS Code`), or merge into one table with an `Editor` column. Do not assume Cursor and VS Code share the same extension set.

VS Code with Copilot is still VS Code: list **all** installed extensions (Copilot, Copilot Chat, and everything else) with the same structured search — there is no separate Copilot-only path.

### Step 1: List installed extensions

**Cursor:**

```bash
cursor --list-extensions --show-versions
```

Fallback: `~/.cursor/extensions/extensions.json` → `identifier.id` + `version`.

**VS Code (including Copilot):**

```bash
code --list-extensions --show-versions
```

Fallback: `~/.vscode/extensions/extensions.json`.

**VS Code Insiders:**

```bash
code-insiders --list-extensions --show-versions
```

Fallback: `~/.vscode-insiders/extensions/extensions.json`.

Output format (all editors): `publisher.name@version` (e.g. `GitHub.copilot@1.250.0`, `golang.go@0.52.2`).

### Step 2: Build structured search query

For each `publisher.name@version`, derive:

| Field | Example (`GitHub.copilot@1.250.0`) | Example (`golang.go@0.52.2`) |
|-------|-----------------------------------|------------------------------|
| `publisher` | `GitHub` | `golang` |
| `name` | `copilot` | `go` |
| `installedVersion` | `1.250.0` | `0.52.2` |
| **Search query** | `publisher:GitHub name:copilot` | `publisher:golang name:go` |

### Step 3: Search URL (always use this pattern)

```
https://agentmesh.knostic.ai/extensions?dir=desc&page=1&q=publisher%3A{publisher}%20name%3A{name}&sort=recent
```

URL-encode `{publisher}` and `{name}` as they appear in the installed id (e.g. `GitHub` → `GitHub`, `copilot-chat` → `copilot-chat`).

Examples:

| Installed | Search query | Notes |
|-----------|--------------|-------|
| `golang.go@0.52.2` | `publisher:golang name:go` | [search](https://agentmesh.knostic.ai/extensions?dir=desc&page=1&q=publisher%3Agolang%20name%3Ago&sort=recent) |
| `GitHub.copilot@…` | `publisher:GitHub name:copilot` | VS Code Copilot core extension |
| `GitHub.copilot-chat@…` | `publisher:GitHub name:copilot-chat` | Copilot Chat; may be `NOT_FOUND` if not yet indexed |
| `eamodio.gitlens@17.12.2` | `publisher:eamodio name:gitlens` | Same in Cursor or VS Code |

Fetch each URL and **parse the rendered search results** (table rows, extension links, risk indicators, versions). Do not call JSON APIs.

From each result row, extract when available:
- Detail link: `https://agentmesh.knostic.ai/extensions/{numeric_id}`
- `extensionId` (may differ in casing, e.g. `golang.Go` vs `golang.go`)
- `version`
- Risk (safe / risky / dangerous / unscanned)

### Step 4: Match and compare version

Pick the row that matches the installed extension:

1. **Publisher + name** (case-insensitive): AgentMesh `publisher` + `name` equal installed segments, OR
2. **Extension id** (case-insensitive): AgentMesh `extensionId` equals installed `publisher.name`

Ignore unrelated rows (e.g. third-party “copilot” extensions when auditing `GitHub.copilot`).

| Status | Condition |
|--------|-----------|
| `MATCH` | Identity match and `installedVersion` === AgentMesh version |
| `VERSION_MISMATCH` | Identity match, versions differ |
| `NOT_FOUND` | No matching row on the search page |
| `PARTIAL` | Identity match but version not visible in parsed results |

### Step 5: Report

```markdown
# Extension audit (AgentMesh)

Editor: VS Code | Cursor | VS Code Insiders | (multiple)
Scanned: {timestamp}
Installed: {count}
Search style: publisher:{publisher} name:{name} (always)

## Summary
| Status | Count |
|--------|-------|
| MATCH | |
| VERSION_MISMATCH | |
| NOT_FOUND | |

## Details
| Editor | Extension | Installed | AgentMesh | Risk | Status | Search | Detail |
|--------|-----------|-----------|-----------|------|--------|--------|--------|
| VS Code | GitHub.copilot | 1.250.0 | … | … | … | [search](https://agentmesh.knostic.ai/extensions?dir=desc&page=1&q=publisher%3AGitHub%20name%3Acopilot&sort=recent) | … |
| Cursor | golang.go | 0.52.2 | 0.52.2 | safe | MATCH | [search](https://agentmesh.knostic.ai/extensions?dir=desc&page=1&q=publisher%3Agolang%20name%3Ago&sort=recent) | [detail](https://agentmesh.knostic.ai/extensions/38145) |
```

Include the structured search URL for every row. When auditing VS Code with Copilot, call out Copilot-related rows explicitly in the summary if any are `NOT_FOUND`, `VERSION_MISMATCH`, or elevated risk.

## Examples

**VS Code + Copilot** — `GitHub.copilot@1.250.0`

1. List via `code --list-extensions --show-versions`.
2. Query: `publisher:GitHub name:copilot` (always).
3. URL: `https://agentmesh.knostic.ai/extensions?dir=desc&page=1&q=publisher%3AGitHub%20name%3Acopilot&sort=recent`
4. Parse results; match `GitHub.copilot` (or case variant); compare version and risk.

**Copilot Chat** — `GitHub.copilot-chat@…`

1. Query: `publisher:GitHub name:copilot-chat` (always).
2. If AgentMesh shows no data, report `NOT_FOUND` and note the extension may not be indexed yet.

**Go (Cursor or VS Code)** — `golang.go@0.52.2`

1. Query: `publisher:golang name:go` (always — never `q=golang.go`).
2. URL: https://agentmesh.knostic.ai/extensions?dir=desc&page=1&q=publisher%3Agolang%20name%3Ago&sort=recent
3. Parse → `golang.Go`, detail https://agentmesh.knostic.ai/extensions/38145

**GitLens** — `eamodio.gitlens@17.12.2` (either editor)

1. Query: `publisher:eamodio name:gitlens` (always).
2. Match and compare versions (marketplace channel may differ).

## Security notes

- Do not use or store AgentMesh API keys for this workflow.
- Treat risk labels as advisory; open the detail page for evidence before recommending removal.
- One structured search per installed extension; same rules for Copilot, chat, and language extensions.
- Cursor and VS Code are separate installs — audit the editor the user cares about, or both with clear labeling.
