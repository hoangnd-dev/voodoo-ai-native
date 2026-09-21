# Infrastructure Design — `caro-online-mvp`

All answers **A** (2026-09-21). No cloud. No shared-infrastructure.md.

## Mapping: logical → infrastructure

| Logical component | Infrastructure |
| --- | --- |
| Next.js App Router + Route Handlers | One Node.js process on the workshop laptop |
| RoomMap, AuthModule | Process RAM |
| TurnTimer | In-process `setTimeout` |
| SseHub | In-process subscriber sets + HTTP `text/event-stream` |
| GameEngine | Same process, pure TS modules |
| Logs | Process stdout (terminal) |

## Environments

| Name | How | State |
| --- | --- | --- |
| Dev | `next dev` | RAM; lost on stop |
| Demo | `next build` then `next start`, one instance | Same |

No staging, no prod, no IaC.

## What is not used

AWS/Azure/GCP, VM, container, K8s, Lambda, RDS/Postgres/Redis/SQLite, SQS/Kafka, nginx/ALB/API Gateway, TLS terminator, CloudWatch/Datadog, shared cluster.

## Ports

Default Next.js HTTP port (typically 3000) on localhost. Two browsers → same origin. SSE must stay on that process (no extra replica).
