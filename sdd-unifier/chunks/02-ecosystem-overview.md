<!--
CHUNK: 02
TITLE: Ecosystem Overview
PROJECT: [Project Name]
VERSION: [X.X]
DEPENDS_ON: 01
PART OF: SDD - [Project Name]
-->

# 6. Ecosystem Overview

<!--
Summary of the platform-wide technology stack and shared infrastructure that all services in this SDD must conform to.
This section is the single source of truth for the implementation constitution and planned technology choices.
-->

| Layer | Technology / Service | Version / Tier | Notes |
|-------|----------------------|----------------|-------|
| Compute / Infra | [On-prem Kubernetes / EKS / AKS / GKE] | [vX.Y] | [Cluster topology, node groups, multi-AZ, etc.] |
| Container Runtime | [containerd / Docker] | [vX.Y] | [Notes] |
| Service Mesh / Ingress | [Istio / Linkerd / NGINX Ingress / Cloud LB] | [vX.Y] | [mTLS, traffic policies] |
| Primary RDBMS | [PostgreSQL / other] | [Version] | [HA topology, replicas, backups] |
| Caching | [Redis / ElastiCache / other] | [Version] | [Cluster mode, persistence, eviction] |
| Event Broker / Streaming | [Kafka / SNS+SQS / RabbitMQ / ActiveMQ] | [Version] | [Topic strategy, retention, partitions] |
| Object Storage | [S3 / MinIO / Azure Blob / other] | [Tier] | [Bucket strategy, lifecycle policies] |
| IAM / AuthN | [Keycloak / Cognito / Auth0 / other] | [Version] | [Realms, federation, OIDC flows] |
| Secrets Management | [Vault / Secrets Manager / Sealed Secrets] | [Version] | [Rotation policy] |
| API Gateway | [Kong / API Gateway / Spring Cloud Gateway] | [Version] | [Auth, rate limiting, routing] |
| CI/CD | [GitHub Actions / GitLab CI / Jenkins / ArgoCD] | [Version] | [Pipeline standards] |
| Observability: Logging | [Loki / ELK / CloudWatch / other] | [Version] | [Retention, indices] |
| Observability: Metrics | [Prometheus + Grafana / Datadog / other] | [Version] | [Scrape interval, dashboards] |
| Observability: Tracing | [OpenTelemetry + Jaeger / Tempo / X-Ray] | [Version] | [Sampling rate] |
| Backend Runtime | [Language + Framework, e.g., Java 21 / Spring Boot 3.4+] | [Version] | [Layering rules] |
| Frontend Stack | [Framework + UI lib, e.g., Angular / Tailwind / PrimeNG] | [Version] | [Design system, primary color] |
| Reporting / BI | [Tool] | [Version] | [Read-replica or warehouse-backed] |

**Ecosystem-level rules:**

- [Rule 1, e.g., timezone]
- [Rule 2, e.g., ID strategy]
- [Rule 3, e.g., service-to-service auth]
- [Rule 4, e.g., secrets handling]

<!-- MASTER: sdd-master.md | PREV: 01-executive-summary-scope-risks.md | NEXT: 03-users-and-use-cases.md -->
