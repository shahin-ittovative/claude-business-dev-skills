<!--
CHUNK: 09
TITLE: Cross-Cutting Concerns
PROJECT: [Project Name]
VERSION: [X.X]
PART OF: LLD - [Project Name]
-->

# 12. Cross-Cutting Concerns

> **Convention:** these are platform-wide rules. Per-service overrides live in `04-implementation/<service>.md`. If a service deviates from a default here, it must justify the override in its own file and link back to this section.

## 12.1 Authentication & Tenant Resolution

| Concern | Choice | Source |
|---------|--------|--------|
| Token issuer | Keycloak realm `[name]` | CLAUDE.md (on-prem default) |
| Token type | JWT (Bearer) | Standard |
| Validation point | API gateway | CLAUDE.md (no per-service JWT validation) |
| Internal service-to-service auth | mTLS via [Istio / Linkerd / NGINX] | CLAUDE.md spirit |
| Tenant ID source | `tenant_id` JWT claim | CLAUDE.md multi-tenancy rule |
| Tenant ID propagation | `X-Tenant-Id` header on internal calls | LLD convention |
| Logging policy | `tenant_id` never logged at INFO; never log PII at INFO | CLAUDE.md hard rule |

## 12.2 Idempotency

| Concern | Choice |
|---------|--------|
| Header | `Idempotency-Key` (max 64 chars) |
| Required on | All write endpoints touching money / wallet / notifications / external providers (CLAUDE.md) |
| Dedup tuple | `(tenant_id, idempotency_key)` |
| TTL | 24 hours |
| Storage | `idempotency_record` table per service |
| Conflict response | 409 + RFC 9457 ProblemDetails (`type: idempotency/conflict`) |
| In-flight handling | Wait + retry (caller pattern); server returns 409 immediately |

## 12.3 Resilience (downstream calls)

> **Library:** Resilience4j (CLAUDE.md default).

| Pattern | Default config | Override mechanism |
|---------|----------------|-------------------|
| Timeout | 5s connect / 30s read | Per-call annotation |
| Retry | 3 attempts, exponential backoff (100ms base, 2x multiplier, 1s max), with jitter | Per-call annotation |
| Circuit breaker | 50% failure rate over 20-call sliding window, 30s open state | Per-instance `CircuitBreakerConfig` bean |
| Bulkhead | Per downstream provider, 10 concurrent calls | Per-provider `BulkheadConfig` |

> **Convention:** retries on idempotent calls only. Non-idempotent calls (without an idempotency key) must not be retried automatically.

## 12.4 Outbox Pattern (mandatory for state-changing events)

- **Table:** `outbox` per service schema (see `05-data-model.md`).
- **Writer:** inside the same transaction as the aggregate write.
- **Publisher:** scheduled job, every 1s, batch up to 100 rows.
- **At-least-once delivery:** publisher does not ack until Kafka returns acknowledgement; consumer dedup is mandatory.
- **Monitoring:** alert if any row's age (`now() - created_at`) exceeds 30s.

## 12.5 Saga Pattern (cross-service transactions)

- **Default style:** choreography (each service reacts to events) per CLAUDE.md.
- **When to use orchestration:** flows with >3 steps, conditional branching, or required central visibility. Orchestrator-owning service named in `04-implementation/<orchestrator>.md`.
- **Compensation:** every saga step has a documented compensation action. Compensation actions are themselves idempotent.
- **Failure handling:** failed compensation triggers an alert; manual intervention via runbook.

## 12.6 Error Model (RFC 9457 ProblemDetails)

| Field | Type | Notes |
|-------|------|-------|
| `type` | string (URI) | Stable, dereferenceable error type identifier |
| `title` | string | Human-readable summary |
| `status` | int | HTTP status code |
| `detail` | string | Specific to this occurrence |
| `instance` | string | The path that produced the error |
| `code` (extension) | string | Internal error code (`<context>-<error>`) |
| `traceId` (extension) | string | OpenTelemetry trace ID |
| `errors` (extension) | array | For validation failures: list of field-level errors |

**Base exception:** `ServiceException` (CLAUDE.md). Every business exception extends it and carries an error code.

**Global handler:** `@RestControllerAdvice` translates exceptions to ProblemDetails.

## 12.7 Logging

| Concern | Choice |
|---------|--------|
| Format | JSON (structured) |
| Mandatory fields | `ts`, `level`, `service`, `traceId`, `spanId`, `tenantId` (never PII), `event`, `attrs` |
| Level for tenant context | DEBUG (never INFO per CLAUDE.md) |
| Aggregation | [Loki / Elasticsearch / other] |
| Hot retention | 30 days |
| Cold retention | 1 year |

## 12.8 Tracing

- **Library:** OpenTelemetry SDK + automatic instrumentation for Spring Boot.
- **Backend:** [Tempo / Jaeger / other].
- **Sampling:** 100% in dev/sit, 10% probabilistic in prod (override with `traceparent` header for forced trace).
- **Context propagation:** W3C Trace Context (`traceparent`, `tracestate` headers).

## 12.9 Configuration

- **Source order:** environment variables > Spring profile properties > defaults.
- **Secrets:** [Vault / AWS Secrets Manager / Kubernetes Secrets — pick one] — never in env vars committed to git.
- **Feature flags:** [system + naming convention].

## 12.10 Health & Readiness

- **Liveness:** `/actuator/health/liveness` — fast in-process check (no DB).
- **Readiness:** `/actuator/health/readiness` — checks DB connectivity, Kafka cluster reachable, schema migrations complete.

<!-- MASTER: lld-master.md | PREV: 08-state-and-rules.md | NEXT: 10-operations.md -->
