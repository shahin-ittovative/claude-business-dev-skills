<!--
CHUNK: 16
TITLE: References
PROJECT: [Project Name]
VERSION: [X.X]
PART OF: LLD - [Project Name]
-->

# 19. References

## 19.1 Source Documents

| Document | Path / URL | Version | Notes |
|----------|------------|---------|-------|
| Related BRD | [path or URL, or `Not applicable`] | [version] | |
| Related SDD | [path or URL, or `Not applicable`] | [version] | |
| Source code repo | [URL] | [commit / branch] | (from-code / hybrid) |

## 19.2 Architectural Decision Records

| ADR ID | Title | Status | Link |
|--------|-------|--------|------|
| AD-01 | [Title] | Accepted | [link] |

> Note: ADRs that affect this LLD's design — even if owned by SDD level — should be cross-linked here for traceability.

## 19.3 OpenAPI Specifications

| Service | Path / URL | Notes |
|---------|------------|-------|
| `[service-a]` | [path/openapi.yaml] | Source of truth for §9 API Contracts |

## 19.4 Event Schemas

| Topic | Schema location | Notes |
|-------|------------------|-------|
| `foo.lifecycle.created` | [schema registry URL or file path] | |

## 19.5 Runbooks

| Runbook | Location | Linked from |
|---------|----------|-------------|
| RB-01 Drain outbox backlog | `10-operations.md` § 13.8 | Alert `OutboxBacklog` |
| RB-02 Replay DLQ | `10-operations.md` § 13.8 | Alert `DLQ rows` |

## 19.6 Threat Model

| Document | Path / URL |
|----------|------------|
| Threat model | [link] |

## 19.7 External References

| Reference | URL |
|-----------|-----|
| RFC 9457 ProblemDetails | https://www.rfc-editor.org/rfc/rfc9457 |
| OpenTelemetry Java SDK | https://opentelemetry.io/docs/languages/java/ |
| Resilience4j docs | https://resilience4j.readme.io/ |
| Flyway docs | https://flywaydb.org/documentation |

## 19.8 Related LLDs (sibling projects, cross-references)

| LLD | Reason for cross-reference |
|-----|----------------------------|
| [LLD path] | [Why related] |

<!-- MASTER: lld-master.md | PREV: 15-open-questions.md | END -->
