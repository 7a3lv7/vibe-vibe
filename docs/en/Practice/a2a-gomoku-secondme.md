# A2A Gomoku MVP: AI vs AI with SecondMe API

> **Audience**: Learners who finished backend API + database chapters  
> **Goal**: Build the **simplest** A2A Gomoku system where:  
> 1) AI agents play each other, 2) humans can watch in real time,  
> 3) headless API-key access is supported, 4) every game is persisted,  
> 5) opponent can be random or a selected registered user.

---

## 1) MVP scope first (ship before perfect)

- **Create match** with `random` or `user` opponent mode
- **Auto-play loop**: each turn calls SecondMe API for a legal move
- **Live spectator view** using SSE (lowest complexity)
- **Headless access** via `X-API-Key`
- **Full audit trail**: save every move and final result

---

## 2) Minimal architecture

- **Backend**: Next.js Route Handlers (or any Node API)
- **Database**: PostgreSQL
- **Realtime**: SSE (Server-Sent Events)
- **AI provider**: SecondMe API (server-side secret)
- **Auth**:
  - Browser: normal platform session auth
  - Headless: API key hash verification

SSE is enough because spectators only need server → client push.

---

## 3) Database schema (minimum viable)

```sql
create table ai_clients (
  id bigserial primary key,
  owner_user_id text not null,
  name text not null,
  api_key_hash text not null unique,
  status text not null default 'active',
  created_at timestamptz not null default now()
);

create index idx_ai_clients_owner on ai_clients(owner_user_id);

create table gomoku_matches (
  id bigserial primary key,
  board_size int not null default 15,
  status text not null default 'running',
  created_by_user_id text not null,
  black_user_id text not null,
  white_user_id text not null,
  black_ai_client_id bigint references ai_clients(id),
  white_ai_client_id bigint references ai_clients(id),
  winner_color text,
  finished_at timestamptz,
  created_at timestamptz not null default now()
);

create index idx_gomoku_matches_created_by on gomoku_matches(created_by_user_id);
create index idx_gomoku_matches_status on gomoku_matches(status);

create table gomoku_moves (
  id bigserial primary key,
  match_id bigint not null references gomoku_matches(id) on delete cascade,
  move_no int not null,
  color text not null,
  x int not null check (x >= 0 and x < 15),
  y int not null check (y >= 0 and y < 15),
  thought text,
  latency_ms int,
  created_at timestamptz not null default now(),
  unique(match_id, move_no),
  unique(match_id, x, y)
);

create index idx_gomoku_moves_match on gomoku_moves(match_id, move_no);
```

---

## 4) API surface

### `POST /api/gomoku/matches`

```json
{
  "opponentMode": "random",
  "opponentUserId": null,
  "boardSize": 15
}
```

- `random`: pick a random registered user (excluding requester)
- `user`: use selected `opponentUserId`
- then start the auto-play loop

### `GET /api/gomoku/matches/:id`

Returns metadata + move history for spectator page.

### `GET /api/gomoku/matches/:id/stream` (SSE)

Events:
- `move` for each new move
- `finish` when game ends

### Headless API-key auth

Header:

```http
X-API-Key: sk_live_xxx
```

Server-side:
1. hash incoming key with server salt
2. lookup active `ai_clients` record
3. return `401` if not found

---

## 5) Core A2A game loop

Per turn:
1. rebuild board from `gomoku_moves`
2. call SecondMe API for current color
3. insert move in transaction
4. check winner / draw and update `gomoku_matches`

Prompt template:

```text
You are a Gomoku AI on a 15x15 board (0-14).
Current color: {{color}}
Move history JSON: {{moves}}
Output JSON only: {"x": number, "y": number, "thought": "optional"}
Coordinate must be empty and legal.
```

Failure handling:
- invalid coordinate → retry once, then fallback to random legal point
- timeout (e.g. 8s) → fallback to random legal point
- unique conflict on occupied cell → retry current turn

---

## 6) Acceptance checklist

- [ ] Can create both `random` and `user` matches
- [ ] AI vs AI runs to completion
- [ ] Human spectators see live move updates
- [ ] Headless API-key calls work (invalid key rejected)
- [ ] Full replay is available from persisted match + move records
