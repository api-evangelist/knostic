---
name: knostic-audit-agent-supply-chain
description: >-
  Use when checking whether an AI agent skill, MCP server, or IDE extension is known to
  Knostic AgentMesh and what its security scan verdict is — vetting a dependency before
  installing it, auditing what is already installed, or looking up an artifact by its
  SHA-256 hash.
api: openapi/knostic-agentmesh-openapi.yml
operations:
  - listSkills
  - getSkill
  - listMcp
  - getMcp
  - listExtensions
  - getExtension
---

# Audit the AI agent supply chain against Knostic AgentMesh

AgentMesh is a reputation service over three catalogs: agent skills (~80,500), MCP servers
(~4,800), and VS Code / IDE extensions (~59,900). Use it to answer "is this thing safe to
install, and has anyone scanned it?"

## Base and auth

- Base URL: `https://agentmesh.knostic.ai/api`
- Read endpoints work **without a key** — responses carry `X-Tier: anonymous`.
- With a key: `Authorization: Bearer <AGENTMESH_API_KEY>` (mint from the AgentMesh console).

## Step 1 — find the artifact

Pick the collection that matches what you are vetting:

| Vetting | Operation | Path |
|---|---|---|
| An agent skill | `listSkills` | `GET /skills` |
| An MCP server | `listMcp` | `GET /mcp` |
| An IDE extension | `listExtensions` | `GET /extensions` |

Search with `q` (case-insensitive ILIKE). Skills and MCP match name + description;
extensions also match publisher.

```
GET /skills?q=browser-use&limit=20
GET /mcp?q=filesystem&limit=20
GET /extensions?q=python&marketplace=vscode&limit=20
```

Narrow with `status` (`dangerous`, `risky`, `safe`, `unscanned`), `source` (skills/MCP
only), or `marketplace` (extensions only). Sort with `sort` + `dir`.

**Prefer hash lookup when you have the bytes.** Every collection accepts `hash`, the
SHA-256 of the archived artifact — this identifies the artifact regardless of how it was
named or republished. For skills and MCP servers it matches across versions and returns
the most recent matching version.

```
GET /skills?hash=<64-hex-sha256>
```

## Step 2 — read the verdict

Fetch the detail record: `getSkill` (`GET /skills/{skill_id}`), `getMcp`
(`GET /mcp/{server_id}`), or `getExtension` (`GET /extensions/{ext_id}`).

For skills and MCP servers, read `scanResults[]` — each entry is one scanner's verdict
(`scannerName`, `scannerVersion`, `result` ∈ `pass|warn|fail|error`, `details`,
`scannedAt`). Pass `?version_id=` to pin a specific version; it defaults to the latest.

For extensions, read `riskStatus` (`pass|warn|fail|gray`), `riskLevel`
(`safe|low|medium|high|critical`), `riskReasons[]`, and `riskEvidence[]` — the last gives
you the actual `snippet` and `file_path` that tripped the flag, which is what you cite in
a review.

## Rules that matter

- **`unscanned` is not `safe`.** A missing verdict means nobody has looked. Treat it as
  unknown and trigger a scan (see the `knostic-scan-on-demand` skill) rather than
  reporting it as clean.
- **The filter vocabulary and the response vocabulary differ.** You filter with
  `dangerous|risky|safe|unscanned` but rows come back with `pass|warn|fail|gray` and
  `low|medium|high|critical`. Map deliberately; never assume they are the same enum.
- **Ids are not global.** `id` is an integer unique only within its own collection.
- Paginate with `page` + `limit` (max 200; default 50) until `page * limit >= total`.

## Errors

Errors are a flat `{"detail": "..."}` object — **not** RFC 9457. `401` missing/invalid key,
`404` not found. See `errors/knostic-problem-types.yml`.
