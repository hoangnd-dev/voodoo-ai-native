# React + TypeScript Instructions

**applyTo:** `frontend/**`, `src/**/*.tsx`, `src/**/*.ts`

## Overview

This application uses **React 18+** with **TypeScript (strict mode)** and a **Vite** build tool. All React code must be type-safe, idiomatic, and accessible.

## Component architecture

- Use `.tsx` for files containing JSX.
- Use `.ts` for pure logic files (utilities, hooks, types).
- Organize by feature or responsibility:
  - `components/` — reusable visual components (Board, Lobby, MatchStatus, etc.)
  - `hooks/` — custom React hooks (useSocket, useSession, etc.)
  - `types/` — shared TypeScript types and interfaces
  - `socket/` — Socket.IO client gateway and event types
  - `session/` — cookie and `localStorage` utilities
  - `utils/` — pure utility functions (win detection helpers, etc.)

## TypeScript enforcement

- **Strict mode on.** Set `"strict": true` in `tsconfig.json`.
- **No implicit `any`.** Every parameter, return type, and variable must have an explicit type.
- **Export types explicitly.** Use `export type { TypeName }` for types and interfaces.
- **Discriminated unions for state.** Prefer union types over optional fields:
  ```typescript
  type GameState = 
    | { status: 'waiting'; board: null }
    | { status: 'playing'; board: Cell[][] }
    | { status: 'ended'; board: Cell[][]; result: 'win' | 'draw' };
  ```

## React patterns

### Components

- Prefer **functional components** with hooks.
- Keep components small and focused on a single responsibility.
- Extract complex logic into custom hooks.
- Use `React.FC<Props>` or arrow functions with explicit prop types:
  ```typescript
  interface BoardProps {
    cells: Cell[][];
    onMove: (row: number, col: number) => void;
    disabled: boolean;
  }

  const Board: React.FC<BoardProps> = ({ cells, onMove, disabled }) => {
    // component body
  };
  ```

### Props and state

- Define prop interfaces at the top of the file.
- Use `React.ReactNode` for children that accept JSX or text.
- Use `keyof typeof` for string literal unions from enums.
- Avoid spreading unknown props; list props explicitly:
  ```typescript
  // Good
  const MyComponent: React.FC<{ label: string; value: string }> = ({ label, value }) => ...

  // Avoid
  const MyComponent: React.FC<Props> = (props) => ...
  ```

### Hooks

- Use `useState<T>()` with explicit type if inference is ambiguous.
- Use `useEffect` with explicit dependency arrays. Do not omit them.
- Extract complex hook logic into separate custom hooks:
  ```typescript
  const useGameState = (roomId: string) => {
    const [state, setState] = useState<GameState>({ status: 'waiting', board: null });
    useEffect(() => {
      // setup side effect
    }, [roomId]);
    return state;
  };
  ```
- Custom hooks should return tuples or named objects, never undefined unless intentional.

## Server snapshot rendering

- **Treat the server `room_snapshot` as the single source of truth.**
- Components render from the snapshot, never from optimistic local state.
- Do not implement game rule validation or move authorization on the client.
- Do not cache old snapshots; replace on every update.
- Example:
  ```typescript
  interface RoomSnapshot {
    id: string;
    owner: Guest;
    players: Guest[];
    game: Game | null;
    status: 'waiting' | 'playing' | 'ended';
  }

  const RoomView: React.FC<{ snapshot: RoomSnapshot }> = ({ snapshot }) => {
    return (
      <div>
        <Board cells={snapshot.game?.board} disabled={snapshot.status !== 'playing'} />
      </div>
    );
  };
  ```

## Accessibility (A11y)

- Every interactive element must have a semantic label or accessible name.
- Board cells must have explicit ARIA labels:
  ```typescript
  <button
    aria-label={`Cell ${row + 1}, ${col + 1}${cell ? ` (${cell})` : ''}`}
    onClick={() => onMove(row, col)}
    disabled={disabled || cell !== null}
  >
    {cell}
  </button>
  ```
- Use semantic HTML: `<button>`, `<form>`, `<fieldset>`, `<legend>`, `<label>`.
- Maintain focus visibility:
  ```css
  button:focus-visible {
    outline: 2px solid currentColor;
    outline-offset: 2px;
  }
  ```
- Test keyboard navigation (Tab, Enter, Arrow keys) for every interactive feature.

## Vietnamese localization (i18n)

- Keep all user-facing copy in Vietnamese.
- Use a simple key-based object for strings or integrate i18next/react-i18next for scale:
  ```typescript
  const vi = {
    lobby: {
      createRoom: 'Tạo phòng',
      joinRoom: 'Tham gia phòng',
      noRoomsAvailable: 'Không có phòng khả dụng',
    },
    game: {
      yourTurn: 'Đến lượt bạn',
      opponentTurn: 'Đến lượt đối thủ',
      youWon: 'Bạn thắng!',
      draw: 'Hoà!',
    },
  };
  ```
- Include Vietnamese copy in error messages, validation, toasts, and state labels.

## Error handling and loading states

- Display meaningful Vietnamese error messages.
- Do not expose stack traces or implementation details to the user.
- Show loading states for async operations (joining a room, placing a mark, etc.).
- Example:
  ```typescript
  type AsyncState<T> = 
    | { status: 'idle'; data: null; error: null }
    | { status: 'loading'; data: null; error: null }
    | { status: 'success'; data: T; error: null }
    | { status: 'error'; data: null; error: string };
  ```

## Performance

- Memoize expensive components with `React.memo()` if they receive the same props repeatedly.
- Use `useCallback` for event handlers passed to child components to prevent unnecessary re-renders.
- Avoid creating new objects or functions in render:
  ```typescript
  // Avoid
  const MyComponent = () => (
    <Child onClick={() => console.log('clicked')} />
  );

  // Good
  const MyComponent = () => {
    const handleClick = useCallback(() => console.log('clicked'), []);
    return <Child onClick={handleClick} />;
  };
  ```

## Testing

Frontend tests are optional for the MVP, but when written:
- Use **Vitest** or **Jest** with **React Testing Library**.
- Test user interactions, not implementation details.
- Write at least happy-path tests for critical features (placing a mark, joining a room).
- Verify that disabled states are applied correctly.
- Example:
  ```typescript
  it('should place a mark on empty cell click', async () => {
    render(<Board cells={emptyBoard} onMove={handleMove} />);
    const cell = screen.getByLabelText(/Cell 1, 1/);
    await userEvent.click(cell);
    expect(handleMove).toHaveBeenCalledWith(0, 0);
  });
  ```

## No client-side move validation

- Do not check if a move is legal on the client.
- Do not enforce turn order or check board state before sending to the server.
- The server is authoritative and will reject invalid moves.
- This simplifies the client and prevents cheating or desync.
