<!--
CHUNK: 10a
TITLE: Detailed Service Spec - [Service Name]
PROJECT: [Project Name]
VERSION: [X.X]
DEPENDS_ON: 09, 07
PART OF: SDD - [Project Name]
-->

# 13.2 Detailed Service Specs

<!--
Repeat this chunk (10a, 10b, 10c, ...) for each service.
Each service follows the exact same structure for predictability and grep-ability.
-->

---

## 13.2.1 [Service Name]

### What

<!-- Concise definition of the service and its bounded context. -->

[Definition.]

### Boundaries

- **Owns:** [Entities / aggregates / data this service is the source of truth for]
- **Does not own:** [Things explicitly outside its boundary]
- **Upstream consumers:** [Who calls this service]
- **Downstream dependencies:** [What this service calls / consumes]

### Input

| Type | Source | Description |
|------|--------|-------------|
| [REST / Event / Schedule / Other] | [Source] | [Description] |

### Business Logic

<!-- Plain-language description of the logic, including state machines for stateful services. -->

[Description of the core logic.]

**State machine (if applicable):**

```text
States: [State A] -> [State B] -> [State C]

Transitions and triggers:
  [State A]   --[Trigger]--> [State B]
  [State B]   --[Trigger]--> [State C]
```

### Output

| Type | Destination | Description |
|------|-------------|-------------|
| [REST response / Event / File / Other] | [Destination] | [Description] |

### Integrations

| Integration | Direction | Protocol | Purpose | Failure Handling |
|-------------|-----------|----------|---------|------------------|
| [System] | [Inbound / Outbound / Sync / Async] | [Protocol] | [Purpose] | [Failure handling] |

### DB Modeling

#### Entity Relationship

> ERD description (tool-agnostic).

```text
Entities (with PKs in []):
  - [entity_a] [pk]
  - [entity_b] [pk]
  - [entity_c] [pk]

Relationships:
  [entity_a] [cardinality] [entity_b]   (FK: [entity_b].[fk_column])
  [entity_b] [cardinality] [entity_c]   (FK: [entity_c].[fk_column])
```

**Mermaid alternative:**

```mermaid
erDiagram
  ENTITY_A ||--o{ ENTITY_B : has
  ENTITY_B ||--o{ ENTITY_C : contains
  ENTITY_A {
    uuid id PK
  }
  ENTITY_B {
    uuid id PK
    uuid entity_a_id FK
  }
  ENTITY_C {
    uuid id PK
    uuid entity_b_id FK
  }
```

#### Tables Design

| Table | Column | Type | Constraints | Notes |
|-------|--------|------|-------------|-------|
| `[table_name]` | `[column]` | [Type] | [Constraints] | [Notes] |
| `[table_name]` | `[column]` | [Type] | [Constraints] | [Notes] |

#### Migration Strategy

- **Tool:** [Flyway / Liquibase]
- **Backward compatibility:** [Approach, e.g., additive-only changes, expand-contract for breaking changes]
- **Data backfill:** [Approach for populating new columns on existing rows]
- **Rollback:** [How to roll back a failed migration]

#### Retention Policy

- `[table_name]`: [Retention rule]
- `[table_name]`: [Retention rule]

#### Archival

- **Cold storage:** [Destination]
- **Format:** [Format]
- **Schedule:** [Schedule]
- **Restore SLA:** [SLA]

#### Data Encryption

- **At rest:** [Approach]
- **In transit:** [Approach]
- **Key management:** [KMS / Vault, rotation policy]
- **PII columns:** [List + masking policy in non-prod]

### Multi-Tenancy Specifications

<!-- Override defaults from section 11.2 only if necessary. -->

- **Strategy override:** [None / specify]
- **Tenant filter:** [How filtered]
- **Cross-tenant queries:** [Policy]

### API Standards

- **Style:** [REST / gRPC / GraphQL]
- **Versioning:** [Approach]
- **Authentication:** [Mechanism]
- **Idempotency:** [Approach]
- **Pagination:** [Approach]
- **Error envelope:** [Schema]

#### List of APIs (Swagger-friendly)

| Method | Path | Summary | Request Body | Response | Auth Scope |
|--------|------|---------|--------------|----------|------------|
| [METHOD] | `[path]` | [Summary] | `[RequestSchema]` | `[ResponseSchema]` | `[scope]` |

### Event-Driven Architecture (If Applicable)

#### Event Model

| Event Name | Producer | Producer Specs | Consumers | Consumer Specs | Schema (Summary) | Delivery Guarantee |
|------------|----------|----------------|-----------|----------------|------------------|---------------------|
| `[EventName]` | [Service] | [Topic, partitions, retention, key] | [Consumer services] | [Consumer group, idempotency] | `[Payload summary]` | [At-least-once / Exactly-once] |

#### Messaging Infra

- **Broker:** [Broker]
- **Schema registry:** [Registry / approach]
- **Serialization:** [Avro / JSON / Protobuf]
- **Topic strategy:** [Naming + partitioning]
- **Retention:** [Retention]
- **DLQ strategy:** [DLQ + replay]

### Constraints

- [Constraint 1]
- [Constraint 2]
- [Constraint 3]

### Error Handling

- **Synchronous APIs:** [Approach]
- **Validation errors:** [Approach]
- **Domain errors:** [Approach]
- **Auth errors:** [Approach]
- **Server errors:** [Approach]
- **Async consumers:** [Approach]
- **Poison messages:** [Approach]

### Observability & Monitoring

#### Logging

- [Format]
- [Mandatory fields]
- [Retention]

#### Metrics

| Metric | Type | Labels | Purpose |
|--------|------|--------|---------|
| `[metric_name]` | [counter / gauge / histogram] | [labels] | [purpose] |

#### Tracing

- [Instrumentation approach]
- [Context propagation]
- [Sampling]

### Developer Notes

- **Recommended patterns:** [Patterns]
- **Avoid:** [Anti-patterns]
- **Testing:** [Test strategy]

### Service-Level Diagrams

#### Implementation Flow Chart

> Description (tool-agnostic).

```text
Inputs:
  - [Input 1]
  - [Input 2]

Flow:
  1. [Step]
  2. [Step]
  3. [Step]
  4. [Step]
```

**Mermaid alternative:**

```mermaid
flowchart TD
  A[Step 1] --> B[Step 2]
  B --> C[Step 3]
```

#### Sequence Diagram (Service-Internal)

> Description (tool-agnostic).

```text
Participants: [P1], [P2], [P3]

  1. [P1] -> [P2]: [message]
  2. [P2] -> [P3]: [message]
  3. [P3] -> [P2]: [response]
  4. [P2] -> [P1]: [response]
```

**Mermaid alternative:**

```mermaid
sequenceDiagram
  participant P1
  participant P2
  P1->>P2: [message]
  P2-->>P1: [response]
```

### Compliance

- **GDPR:** [Lawful basis, retention windows, right-to-erasure flow]
- **PCI-DSS:** [Applicability + approach]
- **ISO 27001 / SOC 2:** [Controls applicable]
- **Local regulations:** [List + how met]

### Deployment Strategy

- **Service-specific override:** [None / specify]
- **Replicas:** [min / max]
- **Strategy:** [Rolling / Blue-Green / Canary]
- **Health checks:** [Probes]
- **Rollback:** [Trigger + approach]

### Future Enhancements

- [Known gap or planned improvement 1]
- [Known gap or planned improvement 2]

<!-- MASTER: sdd-master.md | PREV: 09-services-summary.md | NEXT: 11-performance-and-capacity.md -->
