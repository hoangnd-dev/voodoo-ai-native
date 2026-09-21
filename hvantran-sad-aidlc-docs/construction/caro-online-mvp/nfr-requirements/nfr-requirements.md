# NFR Requirements — `caro-online-mvp`

Source answers (2026-09-21): Q3=A Q4=A Q5=A Q9=A Q10=B Q11=B Q12=C; F1–F5 all A.

## Scalability

| ID | Requirement |
| --- | --- |
| NFR-SC-01 | One Node process. No horizontal scale. |
| NFR-SC-02 | Demo load: a handful of rooms, two browsers per room. |
| NFR-SC-03 | In-memory room map + in-process SSE. Multi-instance is out of scope. |

## Performance

| ID | Requirement |
| --- | --- |
| NFR-PF-01 | No numeric latency SLO. |
| NFR-PF-02 | Two humans on localhost/LAN must see command + SSE snapshot as immediate. |
| NFR-PF-03 | GameEngine unit tests must stay fast (no network, no Next runtime). |

## Availability

| ID | Requirement |
| --- | --- |
| NFR-AV-01 | Process stop or crash deletes all rooms. No backup, no RTO/RPO. |
| NFR-AV-02 | Recovery = start the process, players create/join again. |

## Security

| ID | Requirement |
| --- | --- |
| NFR-SE-01 | Workshop bar, not production hardening. |
| NFR-SE-02 | Passwords stored hashed. Never log secrets. Never commit secrets. |
| NFR-SE-03 | Guest `userId` may live in a cookie. |
| NFR-SE-04 | Localhost HTTP is allowed. HTTPS not mandated. |
| NFR-SE-05 | No rate-limit SLO. No mandated CSRF library. |
| NFR-SE-06 | Every command still checks owner/seat (functional BR). |
| NFR-SE-07 | Security baseline extension **off**. |

## Reliability

| ID | Requirement |
| --- | --- |
| NFR-RE-01 | Server owns turn timeout (`turnId`). Stale jobs ignored. |
| NFR-RE-02 | SSE drop with tab alive: reconnect + GET snapshot (functional Q8=A). |
| NFR-RE-03 | Resiliency baseline extension **off**. |

## Maintainability / test

| ID | Requirement |
| --- | --- |
| NFR-MA-01 | GameEngine unit tests: legal move, win, draw, timeout `turnId`. |
| NFR-MA-02 | A few HTTP + SSE integration tests: create, join, start, move. |
| NFR-MA-03 | Playwright two-browser path **not** required for Code Generation done. |
| NFR-MA-04 | Property-based testing extension **off**. |

## Usability / a11y

| ID | Requirement |
| --- | --- |
| NFR-US-01 | All user-facing copy Vietnamese. |
| NFR-US-02 | Keyboard board, visible focus, readable contrast, per-cell labels / accessible names. |
| NFR-US-03 | WCAG 2.2 AA audit is **not** a release gate. |

## Observability

| ID | Requirement |
| --- | --- |
| NFR-OB-01 | Stdout logs: commands, rejects, timeouts, SSE connect/drop. |
| NFR-OB-02 | No metrics backend, no alerting. |

## Compliance summary (extensions)

| Extension | Status | Rationale |
| --- | --- | --- |
| Security baseline | N/A skipped | Q10=B workshop PoC |
| Resiliency baseline | N/A skipped | Q11=B |
| Property-based testing | N/A skipped | Q12=C |
