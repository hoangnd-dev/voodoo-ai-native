# NFR Design Patterns — `caro-online-mvp`

Answers: Q3=A, Q4=A, F1=A, F2=A, F3=A.

## Resilience

| Pattern | Apply? | How |
| --- | --- | --- |
| SSE reconnect | Yes | Browser `EventSource` auto-reconnect; then GET snapshot |
| HTTP retry / backoff | No | Commands are one-shot. Actor-only Vietnamese error. Player clicks again |
| Circuit breaker | No | One process, no downstream |
| Bulkhead / multi-AZ | No | Workshop; resiliency extension off |

Do not auto-retry POST. A lost 200 after a successful `placeMark` plus retry would double-place.

## Scalability

| Pattern | Apply? | How |
| --- | --- | --- |
| Horizontal scale | No | Single Node instance |
| Room cap | No | Unbounded in-memory Map |
| Sticky sessions / mesh | No | One process owns all SSE sockets |

## Performance

| Pattern | Apply? | How |
| --- | --- | --- |
| In-process engine | Yes | Pure functions on the Next.js server |
| Worker threads | No | 15×15 win check is cheap |
| Snapshot cache | No | Snapshot = current store projection |
| Numeric SLO / cache CDN | No | NFR-PF-01 |

## Security

| Pattern | Apply? | How |
| --- | --- | --- |
| Password hash | Yes | At rest only |
| Command authorization | Yes | Owner/seat on every command |
| Guest cookie | Yes | `userId` only |
| CSRF / CSP / HTTPS redirect / helmet gate | No | Workshop bar Q4=A |

## Observability

Stdout logs for command, reject, timeout, SSE connect/drop. No APM.

## Explicitly absent

Retry library, queue, Redis, reverse proxy, load balancer, circuit breaker, worker pool, room-count limiter.
