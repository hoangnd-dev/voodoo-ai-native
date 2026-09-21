# NFR Design Plan — `caro-online-mvp`

NFR Requirements approved. Fill every `[Answer]:`. **A** is recommended. Extra queues/caches/breakers fight the workshop NFR (one process, no SLO, extensions off).

After answers: write `nfr-design-patterns.md` and `logical-components.md`.

## Execution steps (after answers)

- [x] Analyze NFR requirements + SAD
- [x] Record answers; follow up if mix/depends
- [x] Generate nfr-design-patterns.md
- [x] Generate logical-components.md

---

# Questions

## Question 1

Resilience: how do clients retry?

A) Browser `EventSource` reconnect (already specified). HTTP commands: one shot; actor sees Vietnamese error; player retries by clicking again. No retry library, no circuit breaker.

B) Automatic HTTP retry with backoff on 5xx.

C) Other (please describe after [Answer]: tag below)

[Answer]: _(not a letter — see Follow-up F1)_

## Question 2

Scalability pattern inside the one process?

A) No cap beyond “handful of rooms.” Unbounded in-memory Map. Fine for demo.

B) Hard max rooms (e.g. 20). Reject create with Vietnamese error when full.

C) Other (please describe after [Answer]: tag below)

[Answer]: _(not a letter — see Follow-up F2)_

## Question 3

Performance pattern for GameEngine?

A) Pure in-process functions on the Next.js server. No worker thread, no cache. Snapshot is the current store, not a derived cache.

B) Offload engine to a worker thread.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 4

Security patterns (workshop bar)?

A) Password hash at rest. Guest cookie for userId. Authorize on every command. No CSRF token, no helmet/CSP gate, no HTTPS redirect.

B) Add SameSite cookie + httpOnly as defaults anyway (still localhost HTTP). Still no CSRF/CSP gate.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 5

Logical infrastructure components?

A) None extra. No queue, no Redis, no reverse proxy, no load balancer, no circuit breaker. Turn timer = in-process `setTimeout` keyed by `turnId`. SSE = in-process subscriber set per room.

B) Add a job queue for timeouts.

C) Other (please describe after [Answer]: tag below)

[Answer]: _(not a letter — see Follow-up F3)_

---

# Follow-ups (letter required)

Q3=A, Q4=A locked.

## Follow-up F1 (was Q1 — retry)

**What it decides:** Auto-retry vs human retry.

**A (recommended):** `EventSource` reconnects on its own. A move/start/join is **one HTTP call**. If it fails, show Vietnamese error. Player clicks again. No axios-retry, no circuit breaker.

**Why not B:** Auto-retry on POST can **double-place** a mark if the first write succeeded and the response was lost.

A) Q1 = A

B) Q1 = B

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Follow-up F2 (was Q2 — room cap)

**What it decides:** Guard the in-memory Map or not.

**A (recommended):** No max. Workshop is a handful of rooms. Less code.

**B:** Cap (e.g. 20). Create fails with Vietnamese error. Tiny safety against a runaway tab. Extra rule to test.

A) Q2 = A

B) Q2 = B

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Follow-up F3 (was Q5 — extra components)

**What it decides:** Whether timeouts/SSE sit in-process or behind a queue.

**A (recommended):** `setTimeout` + `turnId`. Per-room Set of SSE writers. No Redis, no queue, no proxy, no breaker. Matches one Node process.

**B:** Job queue for timeouts. Extra moving part. Restart still wipes rooms. No benefit for 20s demo timer.

A) Q5 = A

B) Q5 = B

C) Other (please describe after [Answer]: tag below)

[Answer]: A
