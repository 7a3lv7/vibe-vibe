# A2A 五子棋实战：让 AI 和 AI 自己下棋（SecondMe API）

> **面向人群**：已经完成《进阶篇》后端 API 与数据库章节的同学  
> **目标**：用**最简单**方式实现一个 A2A 五子棋系统：  
> 1) AI 可以互相对弈；2) 人类可实时观战；3) 支持 API Key 无头访问；4) 所有对局全量记录；5) 对手可随机或指定平台注册用户

---

## 1. MVP 目标（先跑通，再优化）

我们不追求“游戏引擎级”复杂度，先做可上线的最小闭环：

- **对局创建**：可选 `random`（随机匹配）或 `user`（指定用户）作为对手来源
- **AI 对 AI 自动落子**：每回合调用 SecondMe API，让当前 AI 只返回一个合法坐标
- **实时观战**：前端通过 SSE 接收新落子事件（最省代码）
- **无头访问**：外部 AI 通过 `X-API-Key` 调用创建对局 / 查询对局
- **全量留痕**：每一步落子和最终胜负都写入数据库

---

## 2. 最小技术方案

- **后端**：Next.js Route Handlers（或任意 Node API）
- **数据库**：PostgreSQL（Drizzle / SQL 均可）
- **实时通道**：SSE（Server-Sent Events）
- **AI 调用**：SecondMe API（服务端持有平台密钥）
- **鉴权**：
  - Web 端：平台登录态（cookie/session）
  - 无头端：`X-API-Key`（服务端哈希比对）

> 为什么选 SSE：观战是“服务端单向推送”，不需要双向聊天室能力，SSE 比 WebSocket 更简单。

---

## 3. 数据库表设计（最小可用）

下面的表已经覆盖需求中的所有关键能力。

```sql
-- 1) AI 客户端（支持 API Key 无头调用）
create table ai_clients (
  id bigserial primary key,
  owner_user_id text not null,             -- 对应平台注册用户
  name text not null,
  api_key_hash text not null unique,       -- 仅存哈希，不存明文
  status text not null default 'active',   -- active / disabled
  created_at timestamptz not null default now()
);

create index idx_ai_clients_owner on ai_clients(owner_user_id);

-- 2) 对局主表
create table gomoku_matches (
  id bigserial primary key,
  board_size int not null default 15,
  status text not null default 'running',  -- running / finished / aborted
  created_by_user_id text not null,
  black_user_id text not null,
  white_user_id text not null,
  black_ai_client_id bigint references ai_clients(id),
  white_ai_client_id bigint references ai_clients(id),
  winner_color text,                       -- black / white / draw
  finished_at timestamptz,
  created_at timestamptz not null default now()
);

create index idx_gomoku_matches_created_by on gomoku_matches(created_by_user_id);
create index idx_gomoku_matches_status on gomoku_matches(status);

-- 3) 落子明细（全量记录）
create table gomoku_moves (
  id bigserial primary key,
  match_id bigint not null references gomoku_matches(id) on delete cascade,
  move_no int not null,                    -- 第几手，从 1 开始
  color text not null,                     -- black / white
  x int not null check (x >= 0 and x < 15),
  y int not null check (y >= 0 and y < 15),
  thought text,                            -- AI 可选思考摘要
  latency_ms int,                          -- 本次 AI 推理耗时
  created_at timestamptz not null default now(),
  unique(match_id, move_no),
  unique(match_id, x, y)
);

create index idx_gomoku_moves_match on gomoku_moves(match_id, move_no);
```

---

## 4. API 设计（含随机/指定对手）

### 4.1 创建对局

`POST /api/gomoku/matches`

请求体：

```json
{
  "opponentMode": "random",
  "opponentUserId": null,
  "boardSize": 15
}
```

说明：

- `opponentMode = random`：从平台注册用户中随机选择一位可用 AI 对手（排除自己）
- `opponentMode = user`：使用 `opponentUserId` 指定对手
- 创建成功后立即启动“自动对弈循环”

---

### 4.2 查询对局（人类观战页）

`GET /api/gomoku/matches/:id`

返回：

- 对局元信息（黑白方、状态、胜负）
- 已落子列表（按 `move_no` 升序）

---

### 4.3 实时观战流（SSE）

`GET /api/gomoku/matches/:id/stream`

SSE 事件：

- `move`：新落子
- `finish`：对局结束

前端页面最小逻辑：

1. 先拉一次 `GET /matches/:id` 拿历史
2. 再连 SSE，收到 `move` 就 append
3. 收到 `finish` 后停止自动刷新

---

### 4.4 API Key 无头访问

无头客户端在 Header 携带：

```http
X-API-Key: sk_live_xxx
```

服务端流程：

1. 读取 `X-API-Key`
2. 计算哈希（如 SHA-256 + server salt）
3. 在 `ai_clients.api_key_hash` 查找 active 记录
4. 找不到则 `401`

建议开放的无头接口：

- `POST /api/gomoku/matches`（创建 AI 对局）
- `GET /api/gomoku/matches/:id`（拉取进度）

---

## 5. A2A 对弈循环（核心）

每回合只做三件事：

1. 从 DB 读取当前棋盘（`gomoku_moves`）
2. 调用 SecondMe API，请当前行动方返回一个合法坐标
3. 事务写入落子，判胜负；若结束则更新 `gomoku_matches`

### 5.1 给 SecondMe 的最小提示词

```text
你是五子棋 AI。棋盘大小 15x15，坐标范围 0-14。
当前执子: {{color}}
历史落子(JSON): {{moves}}
请只输出 JSON: {"x": number, "y": number, "thought": "可选简短说明"}
必须保证坐标未被占用。
```

### 5.2 稳定性兜底（必须有）

- AI 返回非法坐标：重试 1 次；仍失败则随机合法点兜底
- 单步超时：如 8 秒超时，直接随机合法点
- 事务冲突：按 `unique(match_id, x, y)` 防止重复落子，冲突后重试本手

---

## 6. 关键实现建议（保持最简单）

- **不要**一开始做房间系统、聊天、悔棋
- **不要**先上 WebSocket（SSE 已够用）
- **不要**在客户端直连 SecondMe（密钥必须在服务端）
- **先保证可回放**：只要 `gomoku_moves` 完整，UI 随时可重建棋盘

---

## 7. 验收清单

- [ ] 能创建 `random` 和 `user` 两种对局
- [ ] 两个 AI 能自动下到结束
- [ ] 人类观战页能实时看到新落子
- [ ] API Key 无头创建对局可用，错误 key 会被拒绝
- [ ] `gomoku_matches`、`gomoku_moves` 有完整记录，可复盘

如果以上 5 项全部通过，你的 A2A 五子棋 MVP 就已经可用了。
