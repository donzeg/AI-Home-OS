# AI Home OS Implementation Status

**Project stage:** Architecture specification  
**Specification status:** Draft  
**Implementation status:** Not started  
**Last status review:** 2026-09-16

## Current State

Nothing described by the AI Home OS specification has been implemented or validated in this repository yet.

The repository currently contains architecture documents, illustrative pseudocode, proposed data models, proposed APIs, configuration examples, diagrams embedded in Markdown, and contribution templates. It does not currently contain runnable services, client applications, firmware, deployment manifests, database migrations, automated tests, hardware test results, release artifacts, or a reference deployment.

Unless a future file is explicitly marked as runnable and linked from this status document, code and configuration blocks in the specification are illustrative and untested as an integrated system.

## Status Definitions

| Status | Required meaning |
|--------|------------------|
| **Concept** | The goal or idea is documented, but interfaces or behavior remain incomplete. |
| **Specified** | Architecture, expected behavior, interfaces, risks, and acceptance criteria are documented. |
| **Prototype** | Runnable experimental code exists with reproduction instructions and basic tests. |
| **Integrated** | The component works with its adjacent services or hardware in a reproducible environment. |
| **Validated** | Acceptance criteria have passed and measured evidence is linked. |
| **Production-ready** | Security, operations, recovery, deployment, upgrade, and supported-hardware gates have passed. |

Writing detail does not increase implementation status. A component remains `Concept` or `Specified` until runnable evidence exists.

## Capability Status

| Capability | Specification status | Implementation status | Validation evidence |
|------------|----------------------|-----------------------|---------------------|
| Physical and network infrastructure | Specified | Not started | None |
| Sensor layer | Specified | Not started | None |
| Vision system | Specified | Not started | None |
| Audio system | Specified | Not started | None |
| Identity system | Specified | Not started | None |
| Memory system | Specified | Not started | None |
| AI reasoning engine | Specified | Not started | None |
| Context engine | Specified | Not started | None |
| Automation engine | Specified | Not started | None |
| Energy intelligence | Specified | Not started | None |
| Security architecture | Specified | Not started | None |
| API and integration layer | Specified | Not started | None |
| Mobile application | Specified | Not started | None |
| Wall panels | Specified | Not started | None |
| Administration web application | Concept | Not started | None |
| Proxmox reference deployment | Specified | Not started | None |
| Home Assistant OS and AI VM separation | Specified | Not started | None |
| End-to-end deployment | Concept | Not started | None |

`Specified` in this table means that a dedicated chapter exists. It does not claim that every design question in that chapter is resolved.

## Evidence Required to Advance Status

A capability may move beyond `Specified` only when this document links to the relevant evidence:

- Runnable source code and pinned dependencies
- Reproduction or deployment instructions
- Meaningful automated tests
- Integration or hardware test results where applicable
- Measured performance and reliability results
- Security review and threat-control verification
- Failure, recovery, backup, and upgrade tests
- Supported hardware and known limitations

Claims of `Validated` or `Production-ready` require dated evidence and a named release or commit.

## First Implementation Milestone

The first implementation milestone should establish a small vertical slice rather than attempting the complete architecture:

1. Local MQTT broker and Home Assistant bridge
2. One non-sensitive sensor input
3. One non-sensitive, idempotent device action
4. Minimal administration web flow for HA connection, entity mapping, action outcome, and audit inspection
5. Authentication and audit foundations
6. Reproducible local deployment
7. Automated integration test for the complete path
8. Measured failure and recovery behavior

The milestone remains planned until its source, instructions, tests, and results are linked here.

## Planned Voice Integration Decisions

These entries are architecture decisions only and do not indicate installation, configuration, credentials, accounts, or working integration:

| Component | Planned role | Current status | Required evidence before advancement |
|-----------|--------------|----------------|--------------------------------------|
| Self-hosted LiveKit | WebRTC media plane for authorized mobile, browser, and capable wall-panel conversations | Specified; not implemented | Reproducible deployment, room/token isolation tests, network-loss recovery, observability, and proof that local alerts and safety paths remain independent |
| ElevenLabs streaming TTS | Optional premium cloud voice behind the speech-provider router | Specified; disabled; not implemented | Consent and redaction tests, provider-control review, credential isolation, latency/quality evaluation, usage limits, spending ceiling, kill switch, and egress audit evidence |
| Piper | Required local and offline TTS baseline | Specified; not implemented | Reproducible local deployment, voice evaluation, latency measurements, failure recovery, and alert-path validation |
| XTTS | Optional higher-quality local voice profile | Candidate; not implemented | Resource, quality, cloning-consent, latency, and reliability evaluation against Piper and the approved cloud option |
