# SQLite Persistence Instructions

**applyTo:** `backend/**`, `app/domain/guests/**`, `pyproject.toml`

## Overview

**SQLite** persists guest profiles only. Guest rows survive API restart, but rooms and active games are lost (they live in process memory).

**Do not** persist rooms, games, match history, rankings, or replays.

## Architecture

- **Guest store (SQLite):** User profiles (`id`, `display_name`).
- **Room registry (in-memory):** Active rooms, seats, and games.
- **Game state (in-memory):** Board, turns, win/draw status.

## SQLAlchemy 2.x ORM

### Model definition

```python
# backend/app/domain/guests/models.py
from sqlalchemy import Column, String, DateTime
from sqlalchemy.orm import declarative_base
from datetime import datetime, timezone

Base = declarative_base()

class Guest(Base):
    __tablename__ = 'guests'
    
    id = Column(String(36), primary_key=True)  # UUID or custom ID
    display_name = Column(String(128), nullable=False)
    created_at = Column(DateTime, default=lambda: datetime.now(timezone.utc), nullable=False)
    updated_at = Column(DateTime, default=lambda: datetime.now(timezone.utc), onupdate=lambda: datetime.now(timezone.utc), nullable=False)
    
    def __repr__(self):
        return f'<Guest(id={self.id}, display_name={self.display_name})>'
```

### Database initialization

```python
# backend/app/domain/guests/store.py
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from app.domain.guests.models import Base, Guest

DATABASE_URL = 'sqlite:///./caro.db'  # or from env var

engine = create_engine(
    DATABASE_URL,
    connect_args={'check_same_thread': False},  # SQLite only
    echo=False,  # set to True for SQL logging
)

SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# Create tables on startup
Base.metadata.create_all(bind=engine)
```

### Guest store operations

```python
# backend/app/domain/guests/store.py
from sqlalchemy.orm import Session
from app.domain.guests.models import Guest
from datetime import datetime, timezone

class GuestStore:
    def __init__(self, db_url: str):
        self.engine = create_engine(db_url, connect_args={'check_same_thread': False})
        self.SessionLocal = sessionmaker(bind=self.engine)
        Base.metadata.create_all(bind=self.engine)
    
    def ensure(self, guest_id: str, display_name: str) -> Guest:
        """Upsert guest: update if exists, create if not."""
        with self.SessionLocal() as session:
            guest = session.query(Guest).filter(Guest.id == guest_id).first()
            if guest:
                guest.display_name = display_name
                guest.updated_at = datetime.now(timezone.utc)
            else:
                guest = Guest(id=guest_id, display_name=display_name)
                session.add(guest)
            session.commit()
            session.refresh(guest)
            return guest
    
    def get(self, guest_id: str) -> Optional[Guest]:
        """Retrieve guest by ID."""
        with self.SessionLocal() as session:
            guest = session.query(Guest).filter(Guest.id == guest_id).first()
            return guest
    
    def list_all(self) -> list[Guest]:
        """List all guests (for debugging; not used in production)."""
        with self.SessionLocal() as session:
            return session.query(Guest).all()
```

### Usage in Socket.IO handlers

```python
# backend/app/socket/events.py
guest_store = GuestStore(os.getenv('DATABASE_URL', 'sqlite:///./caro.db'))

@sio.event
async def connect(sid, environ):
    # Optionally log connection for debugging
    pass

@sio.event
async def create_room(sid, data):
    guest_id = data.get('guest_id')
    display_name = data.get('display_name')
    
    # Ensure guest is in SQLite
    guest = guest_store.ensure(guest_id, display_name)
    
    # Create room in memory
    room = room_registry.create(owner_id=guest.id, owner_name=guest.display_name)
    
    # Emit snapshot
    snapshot = await snapshot_projector.project(room)
    await sio.emit('room_snapshot', snapshot, to=sid)
```

### REST endpoint for guest ensure

```python
# backend/app/api/routes/guests.py
from fastapi import APIRouter, Response
from pydantic import BaseModel
from app.domain.guests.store import guest_store

router = APIRouter(prefix='/api', tags=['guests'])

class EnsureGuestRequest(BaseModel):
    id: str
    display_name: str

class GuestResponse(BaseModel):
    id: str
    display_name: str
    
    class Config:
        from_attributes = True

@router.post('/guests', response_model=GuestResponse)
async def ensure_guest(payload: EnsureGuestRequest, response: Response):
    """Ensure guest exists in database and set cookie."""
    guest = guest_store.ensure(payload.id, payload.display_name)
    response.set_cookie(
        key='userId',
        value=guest.id,
        httponly=True,
        secure=True,  # set to False in dev
        samesite='lax',
    )
    return guest
```

## Schema

### guests table

| Column | Type | Nullable | Notes |
|--------|------|----------|-------|
| `id` | VARCHAR(36) | NO | Primary key (UUID or custom ID) |
| `display_name` | VARCHAR(128) | NO | Player's Vietnamese name |
| `created_at` | DATETIME | NO | Auto-set on insert |
| `updated_at` | DATETIME | NO | Auto-updated on modify |

**Indexes:**
- Primary key on `id` (automatic)
- Optional: `display_name` for quick lookups (rarely needed at this scale)

## Database file location

- **Development:** `./caro.db` in the project root (local SQLite file).
- **Production:** Use an absolute path or mounted volume, e.g., `/data/caro.db` or environment variable `DATABASE_URL`.

## Migrations (optional for MVP)

For the MVP, schema is simple (one table) and can be created automatically via `Base.metadata.create_all()`. If the schema evolves, use **Alembic**:

```bash
pip install alembic
alembic init migrations
alembic revision --autogenerate -m "Initial schema"
alembic upgrade head
```

For now, not required.

## Backup and data loss

- **Rooms and games are NOT backed up.** They are lost when the API restarts.
- **Guest profiles ARE persistent.** The SQLite file persists.
- For workshop/demo purposes, backup is optional.
- For production, set up SQLite backup policies (e.g., periodic file copy, replication).

## Performance considerations

- SQLite is suitable for the MVP (single process, guest count < 10k expected).
- Query: `guest = session.query(Guest).filter(Guest.id == guest_id).first()` is O(1) if indexed.
- For scale beyond MVP, migrate to PostgreSQL and use connection pooling.

## Thread safety

SQLite with `check_same_thread=False` is safe for Uvicorn with one worker:

```python
engine = create_engine(
    'sqlite:///./caro.db',
    connect_args={'check_same_thread': False},
)
```

Do NOT use this setting with multiple Uvicorn workers. For multi-worker deployment, use PostgreSQL.

## Testing

```python
# test_guest_store.py
import pytest
from app.domain.guests.store import GuestStore

@pytest.fixture
def guest_store():
    # Use in-memory SQLite for tests
    store = GuestStore('sqlite:///:memory:')
    yield store

def test_ensure_creates_guest(guest_store):
    guest = guest_store.ensure('guest-123', 'Hoà Trần')
    assert guest.id == 'guest-123'
    assert guest.display_name == 'Hoà Trần'

def test_ensure_updates_display_name(guest_store):
    guest1 = guest_store.ensure('guest-123', 'Hoà Trần')
    guest2 = guest_store.ensure('guest-123', 'Hoà Trần Updated')
    assert guest1.id == guest2.id
    assert guest_store.get('guest-123').display_name == 'Hoà Trần Updated'
```

## Environment variables

```bash
# .env or os.getenv()
DATABASE_URL=sqlite:///./caro.db  # or postgresql://user:pass@host/db
```

Load in the app:

```python
import os
DATABASE_URL = os.getenv('DATABASE_URL', 'sqlite:///./caro.db')
guest_store = GuestStore(DATABASE_URL)
```

## No match history, rankings, or replays

Do not add these tables:
- ~~`matches` / `games`~~ — Rooms are ephemeral; games are in memory.
- ~~`move_history`~~ — Out of scope.
- ~~`rankings` / `leaderboards`~~ — Out of scope.
- ~~`replays` / `snapshots`~~ — Out of scope.

If these become in-scope, add new tables and persist game snapshots before room cleanup. For now, keep SQLite minimal.
