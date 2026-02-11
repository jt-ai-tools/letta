# 遷移指令 (letta-cloud 核心)

所有指令的工作目錄均為：`apps/core`。

## 環境設定

- 一鍵式環境與資料庫設定：
  - `just ready`

## Postgres 連線

設定 Postgres URI（請根據您的環境進行調整）：

```bash
export LETTA_PG_URI=postgresql+pg8000://postgres:postgres@localhost:5432/letta-core
```

Alembic 將會記錄生效的 URL（例如 `Using database:  postgresql+pg8000://...`）。

## Alembic 基礎指令 (搭配 uv)

- 升級至最新版本：

```bash
uv run alembic upgrade head
```

- 降級一個步驟：

```bash
uv run alembic downgrade -1
```

- 降級至特定的修訂版本 (revision)：

```bash
uv run alembic downgrade <修訂版本_id>
```

- 自動生成新的修訂版本：

```bash
uv run alembic revision --autogenerate -m "簡短描述訊息"
```

- 生成空白修訂版本（用於手動操作）：

```bash
uv run alembic revision -m "手動遷移"
```

## 典型工作流程片段

### 新增/修改欄位

```bash
cd apps/core
just ready                       # 選填，但建議執行
export LETTA_PG_URI=postgresql+pg8000://postgres:postgres@localhost:5432/letta-core
uv run alembic upgrade head      # 確保資料庫已更新至最新狀態
uv run alembic revision --autogenerate -m "add_<欄位名>_to_<資料表名>"
uv run alembic upgrade head
```

### 編輯後重新執行最後一個遷移（僅限本地環境）

```bash
cd apps/core
export LETTA_PG_URI=postgresql+pg8000://postgres:postgres@localhost:5432/letta-core
uv run alembic downgrade -1
uv run alembic upgrade head
```
