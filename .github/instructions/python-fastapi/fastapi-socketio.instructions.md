# FastAPI + Socket.IO Backend Instructions

**applyTo:** `backend/**`, `src/**/*.py`, `pyproject.toml`

## Overview

The backend is a **Python 3.12+** application built with **FastAPI** for HTTP API and **Socket.IO** (via `python-socketio`) for real-time game communication. Uvicorn runs a **single worker** to keep rooms and games in process memory. Guest profiles persist in SQLite.

## Architecture principles

1. **Server is authoritative.** All game rules, room state, and move validation happen on the server.
2. **Handlers are thin.** Socket.IO and REST handlers delegate to domain modules.
3. **Domain is pure.** Game engine, room registry, and guest store are testable independently.
4. **Rooms in memory, guests in SQLite.** Rooms die on restart; guest profiles survive.
5. **Emit after every mutation.** Successful room or game changes trigger a `room_snapshot` push to all players.

## Project structure

```
backend/
  app/
    main.py              # FastAPI + Socket.IO ASGI app
    api/
      routes/
        health.py        # GET /api/health
        guests.py        # POST /api/guests
        rooms.py         # GET /api/rooms (optional)
    socket/
      events.py          # Socket.IO event handlers
      namespaces.py      # Optional: namespace organization
    domain/
      game/
        engine.py        # Pure Caro game logic
        models.py        # Board, Game, GameResult
      rooms/
        registry.py      # Room lifecycle, seats
        models.py        # Room, Seat
      guests/
        store.py         # SQLite guest persistence
        models.py        # Guest, SQLAlchemy ORM
    sync/
      snapshot.py        # StateProjector, canonical room snapshots
      broadcaster.py     # SocketBroadcaster
  tests/
    test_game_engine.py  # pytest for game rules
    test_rooms.py        # pytest for room logic
  pyproject.toml
  uv.lock
```

## FastAPI setup

### main.py — Create the ASGI app

```python
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware
from socketio import ASGIApp, AsyncServer
from app.api.routes import health, guests, rooms
from app.socket.events import register_handlers

app = FastAPI(title='Caro Online', version='1.0.0')

# CORS (dev proxy handles this locally; tighten in production)
app.add_middleware(
    CORSMiddleware,
    allow_origins=['*'],
    allow_credentials=True,
    allow_methods=['*'],
    allow_headers=['*'],
)

# Routes
app.include_router(health.router)
app.include_router(guests.router)
app.include_router(rooms.router, prefix='/api/rooms')

# Socket.IO
sio = AsyncServer(
    async_mode='asgi',
    cors_allowed_origins='*',
)
register_handlers(sio)

app = ASGIApp(socketio_server=sio, socketio_path='/socket.io', other_asgi_app=app)
```

### Run with Uvicorn

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000 --workers 1
```

**Important:** Use `--workers 1` (one worker) so rooms stay in process memory.

## HTTP API (FastAPI routes)

### Response models

Use **Pydantic** models for all requests and responses. Define them explicitly:

```python
from pydantic import BaseModel, Field

class GuestResponse(BaseModel):
    id: str
    display_name: str

class RoomResponse(BaseModel):
    id: str
    owner_id: str
    owner_name: str
    status: str  # 'waiting' | 'playing' | 'ended'
    players: list[GuestResponse]
    game: Optional[GameResponse] = None

class RoomSnapshotResponse(BaseModel):
    room: RoomResponse
    error: Optional[dict[str, str]] = None
```

### Example routes

**POST /api/guests** — Ensure guest profile and set cookie

```python
@router.post('/api/guests', response_model=GuestResponse)
async def ensure_guest(payload: dict, response: Response):
    guest_id = payload.get('id')
    display_name = payload.get('display_name')
    guest = await guest_store.ensure(guest_id, display_name)
    response.set_cookie('userId', guest_id, httponly=True)
    return guest
```

**GET /api/health** — Health check

```python
@router.get('/api/health')
async def health():
    return {'status': 'ok'}
```

**GET /api/rooms** — List joinable rooms (optional; can use Socket.IO instead)

```python
@router.get('/api/rooms', response_model=list[RoomResponse])
async def list_rooms():
    return room_registry.list_joinable()
```

## Socket.IO events

### Design principles

- **One connection per browser tab.** After connect, client sends `userId` and `display_name`.
- **Commands are stateless.** Every event includes the necessary identifiers (e.g., `room_id`).
- **Validation before mutation.** Check guest, room, seat, and action authorization.
- **Emit snapshots after mutations.** All players in the room get the updated state.

### Event handler structure

```python
@sio.event
async def connect(sid, environ):
    """Track connected socket."""
    print(f'Client {sid} connected')

@sio.event
async def subscribe_room(sid, data):
    """Join the Socket.IO room for broadcast."""
    room_id = data.get('room_id')
    sio.enter_room(sid, room_id)

@sio.event
async def create_room(sid, data):
    """Create a new room."""
    guest_id = get_guest_id_from_cookie(sid)  # or from client data
    display_name = data.get('display_name')
    
    # Validate
    if not guest_id or not display_name:
        await sio.emit('error', {'message': 'Thiếu thông tin'}, to=sid)
        return
    
    # Mutate
    room = await room_registry.create(owner_id=guest_id, owner_name=display_name)
    
    # Broadcast
    snapshot = await snapshot_projector.project(room)
    await sio.emit('room_snapshot', snapshot, to=room.id)

@sio.event
async def place_mark(sid, data):
    """Place a mark on the board."""
    guest_id = get_guest_id_from_cookie(sid)
    room_id = data.get('room_id')
    row, col = data.get('row'), data.get('col')
    
    # Validate
    room = room_registry.get(room_id)
    if not room:
        await sio.emit('error', {'message': 'Phòng không tồn tại'}, to=sid)
        return
    
    seat = room.get_seat_for_guest(guest_id)
    if not seat:
        await sio.emit('error', {'message': 'Bạn không trong phòng này'}, to=sid)
        return
    
    # Authorize (is it this guest's turn?)
    if room.game.current_turn != seat.role:
        await sio.emit('error', {'message': 'Không phải lượt của bạn'}, to=sid)
        return
    
    # Enforce rules (server-authoritative)
    try:
        room.game.place_mark(row, col, seat.role)
    except InvalidMoveError as e:
        await sio.emit('error', {'message': str(e)}, to=sid)
        return
    
    # Broadcast
    snapshot = await snapshot_projector.project(room)
    await sio.emit('room_snapshot', snapshot, to=room_id)
```

### Client → server events

| Event | Payload | Notes |
|-------|---------|-------|
| `create_room` | `{ display_name }` | Returns room snapshot |
| `list_rooms` | `{}` | Returns list of rooms |
| `join_room` | `{ room_id, display_name }` | Returns room snapshot |
| `leave_room` | `{ room_id }` | Removes player; deletes empty room |
| `start_game` | `{ room_id }` | Owner only |
| `start_new_game` | `{ room_id }` | Owner only; requires 2 players |
| `place_mark` | `{ room_id, row, col }` | Server enforces turn and rules |

### Server → client events

| Event | Payload | Notes |
|-------|---------|-------|
| `room_snapshot` | Full canonical state | Emitted to room after every mutation |
| `error` | `{ code?, message }` | Vietnamese error message |

## Domain modules

### Game engine (`domain/game/engine.py`)

Keep game rules **pure** and **deterministic**:

```python
from enum import Enum
from dataclasses import dataclass

class Marker(Enum):
    X = 'X'
    O = 'O'

@dataclass
class GameState:
    board: list[list[Optional[Marker]]]  # 15×15
    current_turn: Marker
    result: Optional[str] = None  # 'win' | 'draw' | None

class GameEngine:
    def __init__(self):
        self.state = GameState(
            board=[[None for _ in range(15)] for _ in range(15)],
            current_turn=Marker.X,
        )
    
    def place_mark(self, row: int, col: int, marker: Marker) -> None:
        """Place a mark. Raise InvalidMoveError if illegal."""
        if self.state.result:
            raise InvalidMoveError('Game has ended')
        if self.state.board[row][col] is not None:
            raise InvalidMoveError('Cell occupied')
        if self.state.current_turn != marker:
            raise InvalidMoveError('Not your turn')
        
        self.state.board[row][col] = marker
        
        # Check win
        if self.check_win(row, col, marker):
            self.state.result = 'win'
        elif self.is_full():
            self.state.result = 'draw'
        else:
            self.state.current_turn = Marker.O if marker == Marker.X else Marker.X
    
    def check_win(self, row: int, col: int, marker: Marker) -> bool:
        """Check if placement at (row, col) wins (≥5 in a line)."""
        # Implement horizontal, vertical, diagonal checks
        return any([
            self._check_direction(row, col, marker, (0, 1)),   # horizontal
            self._check_direction(row, col, marker, (1, 0)),   # vertical
            self._check_direction(row, col, marker, (1, 1)),   # diagonal
            self._check_direction(row, col, marker, (1, -1)),  # anti-diagonal
        ])
    
    def _check_direction(self, row: int, col: int, marker: Marker, direction: tuple[int, int]) -> bool:
        """Count consecutive marks in a direction (both ways)."""
        count = 1
        for dr, dc in [(direction[0], direction[1]), (-direction[0], -direction[1])]:
            r, c = row + dr, col + dc
            while 0 <= r < 15 and 0 <= c < 15 and self.state.board[r][c] == marker:
                count += 1
                r, c = r + dr, c + dc
        return count >= 5
    
    def is_full(self) -> bool:
        """Check if all 225 cells are filled."""
        return all(self.state.board[r][c] for r in range(15) for c in range(15))
```

**Test the engine independently:**

```python
# test_game_engine.py
def test_place_mark_occupied():
    engine = GameEngine()
    engine.place_mark(0, 0, Marker.X)
    with pytest.raises(InvalidMoveError, match='Cell occupied'):
        engine.place_mark(0, 0, Marker.O)

def test_win_horizontal():
    engine = GameEngine()
    for i in range(5):
        engine.place_mark(0, i, Marker.X)
        if i < 4:
            engine.place_mark(1, i, Marker.O)
    assert engine.state.result == 'win'
```

### Room registry (`domain/rooms/registry.py`)

```python
from dataclasses import dataclass
from typing import Optional

@dataclass
class Seat:
    player_id: str
    player_name: str
    role: Marker

@dataclass
class Room:
    id: str
    owner_id: str
    owner_name: str
    status: str  # 'waiting' | 'playing' | 'ended'
    seats: list[Seat]
    game: Optional[GameEngine] = None
    
    def add_player(self, player_id: str, player_name: str) -> None:
        if len(self.seats) >= 2:
            raise RoomFullError()
        role = Marker.O if self.seats else Marker.X
        self.seats.append(Seat(player_id, player_name, role))
    
    def get_seat_for_guest(self, guest_id: str) -> Optional[Seat]:
        return next((s for s in self.seats if s.player_id == guest_id), None)
    
    def start_game(self) -> None:
        if len(self.seats) != 2:
            raise InvalidOperationError('Need 2 players')
        self.game = GameEngine()
        self.status = 'playing'
    
    def start_new_game(self) -> None:
        if len(self.seats) != 2:
            raise InvalidOperationError('Need 2 players')
        self.game = GameEngine()
        self.status = 'playing'

class RoomRegistry:
    def __init__(self):
        self._rooms: dict[str, Room] = {}
    
    def create(self, owner_id: str, owner_name: str) -> Room:
        room = Room(
            id=uuid.uuid4().hex[:12],
            owner_id=owner_id,
            owner_name=owner_name,
            status='waiting',
            seats=[Seat(owner_id, owner_name, Marker.X)],
        )
        self._rooms[room.id] = room
        return room
    
    def get(self, room_id: str) -> Optional[Room]:
        return self._rooms.get(room_id)
    
    def list_joinable(self) -> list[Room]:
        return [r for r in self._rooms.values() if r.status == 'waiting' and len(r.seats) < 2]
    
    def delete(self, room_id: str) -> None:
        del self._rooms[room_id]
```

## Dependencies

### pyproject.toml

```toml
[project]
name = "caro-online"
version = "1.0.0"
description = "Online Caro (Gomoku) game"
requires-python = ">=3.12"

dependencies = [
    "fastapi>=0.104.0",
    "uvicorn[standard]>=0.24.0",
    "python-socketio>=5.10.0",
    "python-engineio>=4.8.0",
    "sqlalchemy>=2.0.0",
    "pydantic>=2.0.0",
]

[project.optional-dependencies]
dev = [
    "pytest>=7.0.0",
    "pytest-asyncio>=0.21.0",
    "black>=23.0.0",
    "ruff>=0.1.0",
]
```

### Install dependencies

```bash
uv add fastapi uvicorn python-socketio python-engineio sqlalchemy pydantic
uv add --group dev pytest pytest-asyncio black ruff
```

## Testing

**Prioritize game engine tests:**

```bash
pytest backend/tests/test_game_engine.py -v
```

Cover:
- Move placement and turn alternation
- Occupied cell rejection
- Off-turn rejection
- Win conditions (H, V, diagonal)
- Draw detection
- Game locking after win/draw

Example:
```python
# test_game_engine.py
@pytest.mark.asyncio
async def test_five_in_a_row_wins():
    engine = GameEngine()
    for i in range(5):
        engine.place_mark(0, i, Marker.X)
        if i < 4:
            engine.place_mark(1, i, Marker.O)
    assert engine.state.result == 'win'

@pytest.mark.asyncio
async def test_draw_on_full_board():
    engine = GameEngine()
    # Fill board without a winner
    # ...
    assert engine.state.result == 'draw'
```

## Vietnamese error messages

Keep error messages in Vietnamese, clear, and actionable:

```python
error_messages = {
    'room_not_found': 'Phòng không tồn tại',
    'room_full': 'Phòng đầy, không thể tham gia',
    'not_in_room': 'Bạn không trong phòng này',
    'not_your_turn': 'Không phải lượt của bạn',
    'cell_occupied': 'Ô này đã có dấu',
    'game_ended': 'Trò chơi đã kết thúc',
    'invalid_operation': 'Hành động không hợp lệ',
}
```

Return to client:
```python
await sio.emit('error', {
    'message': error_messages.get('not_your_turn')
}, to=sid)
```
