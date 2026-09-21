# Infrastructure Design Plan — `caro-online-mvp`

NFR Design approved. Fill every `[Answer]:`. **A** is recommended. Cloud/DB/queue/LB contradict locked SAD + NFR (in-memory, one process, SSE).

After answers: write `infrastructure-design.md` and `deployment-architecture.md`. Skip `shared-infrastructure.md` unless Q7 says share.

## Execution steps (after answers)

- [x] Analyze NFR design + SAD
- [x] Record answers; follow up if mix/depends
- [x] Generate infrastructure-design.md
- [x] Generate deployment-architecture.md
- [x] shared-infrastructure.md only if Q7 is not A

---

# Questions

## Question 1

Deployment environment?

A) Developer laptop / workshop machine. `next dev` for work, `next start` for demo. No AWS/Azure/GCP.

B) Single cloud VM later; still one Node process.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 2

Compute?

A) One Node.js process. Default Next.js size. No containers, no Kubernetes, no Lambda, no autoscaling.

B) Docker Compose one container, still one process.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 3

Storage?

A) Process RAM only (RoomMap, accounts). No Postgres, no Redis, no disk DB. Restart wipes state.

B) SQLite file on disk (conflicts with in-memory lock unless you explicitly override SAD).

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 4

Messaging / async?

A) None. Timeouts = in-process `setTimeout`. Snapshots = in-process SseHub. No SQS, no Kafka, no Redis pub/sub.

B) External pub/sub (conflicts with F3=A).

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 5

Networking?

A) Loopback / LAN. Browser talks to `localhost:<port>` same origin. No load balancer, no API Gateway, no TLS termination.

B) Reverse proxy (nginx) in front, still one upstream.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 6

Monitoring?

A) Terminal stdout. No CloudWatch, no Datadog, no alerts.

B) Optional local log file besides stdout. Still no pager.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 7

Shared infrastructure with other apps?

A) None. This workshop app owns the port and the process. No shared cluster.

B) Share a workshop Docker network with other team apps.

C) Other (please describe after [Answer]: tag below)

[Answer]: A
