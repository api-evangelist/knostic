---
name: knostic-scan-on-demand
description: >-
  Use when an agent skill or IDE extension is unscanned or stale in Knostic AgentMesh and
  you need a fresh security verdict — triggering an on-demand scan, polling it to
  completion, and managing the 30-per-day scan quota.
api: openapi/knostic-agentmesh-openapi.yml
operations:
  - scanSkill
  - scanExtension
  - getScan
  - listScans
---

# Trigger and poll an on-demand AgentMesh scan

When `knostic-audit-agent-supply-chain` returns `unscanned` — or a verdict older than the
artifact's latest version — request a fresh scan. This is the only write surface on the
API and it is quota-governed.

## Auth is mandatory here

Unlike the catalog reads, **every scan operation requires an API key**:
`Authorization: Bearer <AGENTMESH_API_KEY>`. Mint one from the AgentMesh console.

## Step 1 — check your budget first

```
GET /scans
```

`listScans` returns your scan history (newest first) and, importantly, the
**`X-Scan-Limit-Remaining`** response header — your remaining daily scan credits. Read it
before spending one. Paginate with `page` + `limit` (max 100, default 20).

## Step 2 — trigger the scan

| Target | Operation | Path | Slug format |
|---|---|---|---|
| Skill | `scanSkill` | `POST /scan/skill/{slug}` | `owner:repo` (root-level) or `owner:repo:skill_name` (nested) |
| Extension | `scanExtension` | `POST /scan/extension/{slug}` | `marketplace:extension_id` |

```
POST /scan/skill/anthropics:skills:algorithmic-art
POST /scan/extension/vscode:ms-python.python
```

Both return immediately — this is a **submit-and-poll** contract, not a synchronous scan:

```json
{"scan_id": 1, "status": "pending", "position_in_queue": 1, "estimated_seconds": 30}
```

## Step 3 — poll to completion

```
GET /scans/{scan_id}
```

Poll `getScan` until `status` is `completed` or `failed`. While `pending`/`running` the
response carries queue position and a time estimate — **use `estimated_seconds` to set
your polling interval; do not hammer the endpoint.**

On completion read `result`, and note the result vocabulary differs by target type:

- Skill scans: `pass` | `warn` | `fail`
- Extension scans: `safe` | `low` | `medium` | `high` | `critical`

`details` carries the scanner output (matches, files scanned). `error` is populated only
when `status` is `failed`.

## Rules that matter

- **Scans are scoped to your key.** Reading a `scan_id` created by a different API key
  returns `403`. Only poll ids your own key received.
- **The quota is 30 scans per day per key**, shared across skills and extensions. Exceeding
  it returns `429 Daily scan limit exceeded (30/day)`.
- **Distinguish `429` from `503`.** `503 Scan queue is full` is transient backpressure, not
  quota exhaustion — retry with backoff and it does not cost a credit. A `429` means wait
  for the daily reset.
- **There is no idempotency key.** Re-issuing the same `POST /scan/...` creates a *new*
  scan job and spends *another* credit. Record the `scan_id` from the first call and poll
  it; never retry the POST blindly on a timeout.
- There is no webhook or callback — polling is the only completion signal.

## Errors

Flat `{"detail": "..."}` envelope. `401` missing/invalid key, `403` scan belongs to another
key, `404` target or scan not found, `429` quota, `503` queue full. See
`errors/knostic-problem-types.yml`.
