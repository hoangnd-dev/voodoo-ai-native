# NFR Requirements Plan — `caro-online-mvp`

Functional Design approved. Fill every `[Answer]:`. Recommended option is **A** unless noted.

Locked already (do not reopen): same-origin monolith, in-memory store, single instance, SSE push, HTTP commands.

After answers: write `nfr-requirements.md` and `tech-stack-decisions.md`.

## Execution steps (after answers)

- [x] Analyze functional design + SAD
- [x] Record answers; follow up if mix/depends
- [x] Generate nfr-requirements.md
- [x] Generate tech-stack-decisions.md
- [x] Update Extension Configuration in aidlc-state.md

---

# Questions

## Question 1

Expected load for this workshop MVP?

A) One process. A handful of rooms. Two browsers per demo room. No horizontal scale.

B) Tens of concurrent rooms on one process is still OK; still no multi-instance.

C) Other (please describe after [Answer]: tag below)

[Answer]: _(not a letter — see Follow-up F1)_

## Question 2

Performance target?

A) No numeric SLO. Local/LAN demo: command and SSE snapshot feel immediate to two humans. Engine tests must stay fast.

B) Document p95 command latency (e.g. under 200ms on localhost).

C) Other (please describe after [Answer]: tag below)

[Answer]: _(not a letter — see Follow-up F2)_

## Question 3

Availability and recovery?

A) Process down = rooms gone. No failover, no backup, no RTO/RPO. Restart and recreate rooms.

B) Persist rooms so a restart restores lobby (conflicts with SAD in-memory lock).

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 4

Security bar for MVP?

A) Workshop: hashed passwords, guest id in cookie, no secrets in git, no HTTPS mandate on localhost, no rate-limit SLO, no CSRF library mandated yet. Authorize owner/seat on every command.

B) Treat as production: HTTPS, CSRF, locked-down cookies, rate limits, security headers as blocking.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 5

Language and same-origin runtime? (SSE + in-process store)

A) TypeScript on Node. One Next.js (or equivalent) process: UI + command routes + SSE. Recommended for this workshop.

B) Java + Spring Boot, server + templates or same-origin static UI.

C) C# + ASP.NET, same-origin.

D) Python + FastAPI, same-origin static UI.

E) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 6

How is the Vietnamese UI built inside that process?

A) Next.js App Router pages + client components for board/SSE (pairs with Q5=A).

B) Server-rendered HTML + small page scripts. No SPA framework.

C) Separate SPA bundle still served by the same process.

D) Other (please describe after [Answer]: tag below)

[Answer]: _(not a letter — see Follow-up F3)_

## Question 7

Automated testing expectation before Code Generation is “done”?

A) Unit tests for GameEngine (legal move, win, draw, timeout `turnId`). A few HTTP/SSE integration tests for create-join-start-move. No full browser suite required to ship the slice.

B) Also Playwright (or equivalent) two-browser happy path.

C) Other (please describe after [Answer]: tag below)

[Answer]: _(not a letter — see Follow-up F4)_

## Question 8

Accessibility bar?

A) Keyboard board, visible focus, contrast, per-cell labels, Vietnamese accessible names. No full WCAG audit gate.

B) WCAG 2.2 AA as a blocking release gate.

C) Other (please describe after [Answer]: tag below)

[Answer]: _(not a letter — see Follow-up F5)_

## Question 9

Observability?

A) Stdout logs for commands, rejects, timeouts, SSE connect/drop. No metrics backend, no alerting.

B) Structured JSON logs + request ids. Still no pager.

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 10

Security baseline extension (blocking SECURITY rules)?

A) Yes — enforce as blocking.

B) No — skip. Suitable for this workshop PoC. Recommended.

C) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 11

Resiliency baseline extension?

A) Yes — apply as design-time guidance.

B) No — skip. Recommended for this workshop.

C) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 12

Property-based testing extension?

A) Yes — all PBT rules blocking.

B) Partial — PBT only for pure GameEngine (win/draw/legal). Recommended extra if time.

C) No — skip all PBT. Fine if workshop time is tight.

D) Other (please describe after [Answer]: tag below)

[Answer]: C

---

# Follow-ups (letter required)

Q3=A, Q4=A, Q5=A, Q9=A, Q10=B, Q11=B, Q12=C are locked.

## Follow-up F1 (was Q1 — load)

**What it decides:** How many rooms/browsers we design for. Drives “single Node process is enough” vs premature clustering.

**A (recommended):** Demo scale. A few rooms, two browsers each. No multi-instance. Matches SAD in-memory + SSE on one process.

**B:** Still one process, but you expect many rooms at once. Same architecture; just do not assume “only one demo room.”

A) Q1 = A

B) Q1 = B

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Follow-up F2 (was Q2 — performance)

**What it decides:** Whether we write a latency number testers must measure, or just “feels fine in the demo.”

**A (recommended):** No p95. Two humans on LAN. Engine unit tests stay fast. Workshop has no load lab.

**B:** You want a number (e.g. localhost p95 command under 200ms) in the NFR doc and later tests.

A) Q2 = A

B) Q2 = B

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Follow-up F3 (was Q6 — UI shape)

**What it decides:** How Vietnamese pages are built **inside** the one Next.js process (Q5=A already locked).

**A (recommended):** App Router pages + client components for the 15×15 board and EventSource. Fastest path with Q5=A. SSE and commands stay same origin.

**B:** Classic HTML + little JS. More wiring by hand.

**C:** Vite/React SPA still served by Next/Node. Extra bundling, same deploy.

A) Q6 = A

B) Q6 = B

C) Q6 = C

D) Other (please describe after [Answer]: tag below)

[Answer]: A

## Follow-up F4 (was Q7 — tests)

**What it decides:** What “Code Generation done” means for tests.

**A (recommended):** Engine unit tests + a few HTTP/SSE integration tests. Enough to prove rules and sync. No mandatory two-browser Playwright for the slice.

**B:** Also a Playwright (or equivalent) path: two contexts, create/join/win. Stronger demo proof, more time.

A) Q7 = A

B) Q7 = B

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Follow-up F5 (was Q8 — accessibility)

**What it decides:** Whether a11y is “must for the board” or a full WCAG release gate.

**A (recommended):** Keyboard, focus, contrast, cell labels, Vietnamese names. Matches product brief. No audit report as a blocker.

**B:** WCAG 2.2 AA blocking. Needs audit evidence; heavy for a one-day MVP.

A) Q8 = A

B) Q8 = B

C) Other (please describe after [Answer]: tag below)

[Answer]: A
