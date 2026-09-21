# Unit of Work Dependencies

| Unit | Depends on | Communication |
| --- | --- | --- |
| `caro-online-mvp` | none | Internal module calls + in-process SSE |

No other units. Client and Game Service are modules inside this unit, not separate UOWs.
