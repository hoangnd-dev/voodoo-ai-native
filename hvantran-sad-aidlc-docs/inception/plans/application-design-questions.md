# Application Design Questions

**Status:** recorded 2026-09-20 — Q1=A, Q2=B, Q3=A, Q4=A. Locked into SAD v0.2.

Answer in this file using `[Answer]:` tags. SAD already records a logical architecture. These questions only close remaining physical choices. Do not block SAD approval on stack if team accepts TBU.

## Question 1

Where should room and game state live for the workshop day?

A) In-memory in one Game Service process (Phase 1 meeting notes)

B) Hosted database as source of truth (product brief NFR)

C) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 2

How should the two browsers stay in sync?

A) Client polling of `getSnapshot`

B) Server push (WebSocket or SSE)

C) Hosted realtime product (choose in NFR Requirements)

D) Other (please describe after [Answer]: tag below)

[Answer]: B

## Question 3

How is the Game Service hosted relative to the Game Client?

A) Same modular monolith / same origin

B) Separate API process, client is a SPA

C) BaaS (client talks to hosted backend; still treat Game Service as logical container)

D) Other (please describe after [Answer]: tag below)

[Answer]: A

## Question 4

On refresh or tab close during a match, what should happen?

A) Identity cookie may remain; board resume is not guaranteed (product brief)

B) Rejoin same seat and board using cookie userId (meeting notes)

C) Other (please describe after [Answer]: tag below)

[Answer]: A