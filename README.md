# Knostic

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Knostic secures the AI agent supply chain inside the enterprise. Its platform discovers shadow AI, coding assistants, MCP servers and IDE extensions in use across an organization, then scans them for prompt injection, data exfiltration, secret and PII leakage, and destructive commands. Knostic runs AgentMesh, a public reputation and threat-intelligence service that continuously discovers, tracks and scans AI agent skills, Model Context Protocol servers, and VS Code / IDE marketplace extensions — roughly 80,500 skills, 4,800 MCP servers and 59,900 extensions as of July 2026 — and exposes that catalog with per-version scan verdicts through a REST API whose read endpoints answer anonymous callers. Knostic also ships a substantial open-source portfolio: AgentSonar (shadow-AI network detection), OpenAnt (LLM-based vulnerability discovery), MCP-Scanner, and security and telemetry plugins for OpenClaw agents.

**Areas:** Company · Security · Artificial Intelligence · AI Agents · Agent Security · Supply Chain Security · MCP · Threat Intelligence · Developer Tools · Shadow AI

## Knostic on APIs.io

Everything profiled for Knostic is published on APIs.io rather than in this repository. Start at the provider page — **[apis.io/providers/knostic/](https://apis.io/providers/knostic/)** — or go straight to one of the APIs or properties below.

### APIs (4)

- [Knostic Extensions API](https://apis.io/apis/knostic/knostic-extensions-api/) — VS Code / IDE marketplace extensions with risk assessment
- [Knostic MCP API](https://apis.io/apis/knostic/knostic-mcp-api/) — Model Context Protocol servers discovered and scanned by AgentMesh
- [Knostic Scans API](https://apis.io/apis/knostic/knostic-scans-api/) — On-demand security scans and scan history (API key required)
- [Knostic Skills API](https://apis.io/apis/knostic/knostic-skills-api/) — AI agent skills (SKILL.md) discovered and scanned by AgentMesh

### Published properties

Each property below is a page on APIs.io carrying the machine-readable artifact and what it says.

#### MCP servers (1)

Model Context Protocol servers that expose the API to agents.

- [knostic-agentmesh](https://apis.io/servers/knostic/knostic-agentmesh/)

#### Agentic access (1)

Which operations an agent can call, which act, and which need a human in the loop.

- [Knostic Agentic Access](https://apis.io/agentic-access/knostic/knostic-agentic-access/)

#### Examples (3)

Example requests and responses.

- [Knostic List Extensions Response](https://apis.io/examples/knostic/knostic-list-extensions-response/)
- [Knostic List Mcp Response](https://apis.io/examples/knostic/knostic-list-mcp-response/)
- [Knostic List Skills Response](https://apis.io/examples/knostic/knostic-list-skills-response/)

#### Collections (4)

Postman and Open Collection request collections.

- [Knostic AgentMesh extensions API](https://apis.io/collections/knostic/open-knostic-extensions-api/)
- [Knostic AgentMesh extensions mcp API](https://apis.io/collections/knostic/open-knostic-mcp-api/)
- [Knostic AgentMesh extensions scans API](https://apis.io/collections/knostic/open-knostic-scans-api/)
- [Knostic AgentMesh extensions skills API](https://apis.io/collections/knostic/open-knostic-skills-api/)

#### Overlays (1)

OpenAPI overlays, including conversational phrasing for each operation.

- [Knostic Agentmesh Overlay](https://apis.io/overlays/knostic/knostic-agentmesh-overlay/)

#### Security (3)

Authentication, trust centre, and vulnerability disclosure.

- [Knostic Authentication](https://apis.io/security/knostic/knostic-authentication/)
- [Knostic Domain Security](https://apis.io/security/knostic/knostic-domain-security/)
- [Knostic Trust Center](https://apis.io/security/knostic/knostic-trust-center/)

#### Plans and pricing (1)

Published plans, tiers and prices.

- [Knostic Plans Pricing](https://apis.io/plans/knostic/knostic-plans-pricing/)

#### Rate limits (1)

Published request limits.

- [Knostic Rate Limits](https://apis.io/rate-limits/knostic/knostic-rate-limits/)

## Where to find this API

Links to the company's own pages. This repository publishes no copy of their specifications — the canonical document is theirs and lives at the URLs below.

- [Website](https://www.knostic.ai/)
- [Documentation](https://agentmesh.knostic.ai/api)
- [API reference](https://agentmesh.knostic.ai/api)
- [Pricing](https://www.knostic.ai/pricing)
- [Support](https://www.knostic.ai/contact)
- [Blog](https://www.knostic.ai/blog)
- [Security](https://www.knostic.ai/security)
- [Compliance](https://security.knostic.ai/)
- [Terms of service](https://www.knostic.ai/terms-of-service)
- [Privacy policy](https://www.knostic.ai/privacy-policy)
- [GitHub](https://github.com/knostic)

## API Evangelist rating

**Kin Score 56.2** — strong · rubric 0.25.0 · scored 2026-10-23

| Facet | Score |
|---|---|
| Discoverability | 73.2 |
| Contract quality | 56.6 |
| Contract governance | 4.5 |
| Operational transparency | 60.5 |
| Developer ergonomics | 40.5 |
| Access clarity | 85.5 |

**Agent readiness 35.6** — agent-ready

| Agent-readiness dimension | Result |
|---|---|
| Spec Presence | yes |
| Agentic Access | derived (partial credit) |
| Reversibility Documented | — |
| MCP Server | — |
| Auth Clarity | bearer (partial credit) |
| Idempotency | — |
| Error Semantics | verified |
| OpenAPI Examples | verified |
| Rate Limit Signal | documented (partial credit) |
| Event Surface Described | — |
| Agent Skills | yes |
| Well Known Catalog | — |
| Consent Identity | — |
| Agent Card | — |
| Dry Run Mode | — |
| Delegated Identity | — |
| Protected Resource Metadata | — |
| Dynamic Client Registration | — |
| Agentic Commerce | — |
| Pagination Ratio | na |
| Operation Distinctiveness | distinct |

**Regulatory alignment 27.3** — Horizontal (data, software, accessibility, platform)

The rubric is published at [github.com/api-evangelist/kin-score](https://github.com/api-evangelist/kin-score). The per-check breakdown behind these numbers — which checks were earned, partial or missed — along with score history and how this provider compares to its peers, is available at [apis.io/providers/knostic/](https://apis.io/providers/knostic/).

## How this profile is made

API Evangelist profiles a company's public API surface the way a developer would find it: the website, developer portal and documentation, the specifications it publishes, its public repositories, and its pricing, status and changelog pages. From that it derives the properties above — contracts split per API, workflows, schemas, collections, governance rules, plans and rate limits — and scores them against the published [Kin Score rubric](https://github.com/api-evangelist/kin-score).

The artifacts are published on [APIs.io](https://apis.io), not in this repository. This repository is the public record for Knostic: the place to say something is wrong, missing or out of date, and to ask for a re-score or a removal.

## Open an issue

- **Something about this listing** — a wrong description, a missing API, a stale artifact or a question about the rating: [open an issue on this repository](https://github.com/api-evangelist/knostic/issues/new).
- **A new API, or one that is not in the catalog yet** — [submit it on APIs.io](https://apis.io/add/).
- **Anything else** — [the APIs.io inbox](https://github.com/api-search/inbox/issues/new) or [info@apievangelist.com](mailto:info@apievangelist.com).
