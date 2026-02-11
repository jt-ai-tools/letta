---
name: 資料庫遷移與結構定義變更
description: >-
  管理 letta-cloud 核心應用程式中 Alembic 資料庫遷移與結構定義 (schema) 變更的工作流程與指令，
  包含使用 uv、just、LETTA_PG_URI，以及在 SQLite 與 Postgres 之間切換。
---

# 資料庫遷移與結構定義變更 (letta-cloud 核心)

當您需要變更資料庫結構定義 (schema) 或在 letta-cloud 儲存庫的 `apps/core` 中對 Alembic 遷移進行除錯時，請使用此技能。

此技能假設：
- 工作目錄：`apps/core`
- 遷移工具：位於 `apps/core/alembic` 的 Alembic
- Python 執行工具：`uv`
- 輔助工具：使用 `just ready` 進行環境與資料庫設定

## 快速上手

1. 確保環境已就緒：
   - `just ready`
2. 進行 Postgres 遷移時，請設定：
   - `export LETTA_PG_URI=postgresql+pg8000://postgres:postgres@localhost:5432/letta-core`
3. 進行 ORM/結構定義 (schema) 變更。
4. 自動生成遷移檔案：
   - `uv run alembic revision --autogenerate -m "<簡短描述訊息>"`
5. 套用遷移：
   - `uv run alembic upgrade head`

精確的指令與變體請參閱 `references/migration-commands_zh_TW.md`。

## 標準工作流程

### 1. 新增或修改欄位 (ORM 優先)

1. 確定 ORM 模型 (model) 與資料表 (table)。
2. 在 `letta/orm/...` 中更新 SQLAlchemy 模型：
   - 優先使用現有的 mixins（例如 `ProjectMixin`），而非重複定義欄位。
3. 如果依賴項目或環境有變動，請執行 `just ready`。
4. 若要針對 Postgres 進行遷移，請確保已設定 `LETTA_PG_URI`。
5. 使用 `uv` 自動生成 Alembic 修訂版本 (revision)。
6. 檢查 `alembic/versions/` 下生成的檔案：
   - 確認 `op.add_column` / `op.alter_column` 符合預期。
7. 使用 `uv run alembic upgrade head` 套用遷移。

此模式適用於透過 `ProjectMixin` 新增 `project_id` 欄位等變更。

### 2. 資料回填 (Data backfill) / 一次性資料遷移

1. 確保結構定義變更（如有）已反映在 ORM 與 Alembic 中。
2. 建立一個**不帶**自動生成的 Alembic 修訂版本（或編輯自動生成的檔案），並在 `upgrade()` 中加入 Python 邏輯：
   - 使用 `op.get_bind()` 與 SQLAlchemy Core/SQL 來回填資料。
3. 保持 `downgrade()` 簡單且安全（理想情況下是可逆的）。
4. 在設定好 `LETTA_PG_URI` 的情況下，使用 `uv run alembic upgrade head` 針對 Postgres 執行。

### 3. 修復錯誤的遷移

常見情況：
- 遷移僅在 SQLite 上失敗（由於 ALTER 约束限制）。
- 在指向 SQLite 而非 Postgres 時生成了遷移。

工作流程：
1. 在 `alembic/versions/` 中找出失敗的修訂版本。
2. 如果失敗是 SQLite 特有的，請透過匯出 `LETTA_PG_URI` 並重新執行升級，優先針對 Postgres 執行遷移。
3. 如果邏輯有誤，請建立一個**新的**遷移來解決問題，而非編輯已套用的修訂版本（特別是在共享環境中）。
4. 對於純本地/開發環境的歷史紀錄，您可以刪除並重新生成遷移，但前提是沒有其他人依賴這些遷移。

關於 SQLite 的特定問題，請參閱 `references/sqlite-vs-postgres-gotchas_zh_TW.md`。

### 4. 在 SQLite 與 Postgres 之間切換

Alembic 會根據 `letta.settings.DatabaseChoice` 與環境變數來選擇引擎。

一般規則：
- 對於本地開發的有狀態執行，`just ready` 會處理基礎遷移。
- 對於結構定義設計與類生產環境的遷移，優先選用 Postgres 並設定 `LETTA_PG_URI`。

針對 Postgres 遷移的工作流程：
1. `export LETTA_PG_URI=postgresql+pg8000://postgres:postgres@localhost:5432/letta-core`
2. 在 `apps/core` 目錄下執行：
   - `uv run alembic upgrade head`
   - `uv run alembic revision --autogenerate -m "..."`

### 5. 重設本地 Postgres 以生成乾淨的遷移

如果您的本地 Postgres 資料庫與主分支 (main) 產生偏離（例如：套用了已不存在的遷移，或結構定義過舊），您可以重設它以生成乾淨的遷移。

從儲存庫根目錄 (`/Users/sarahwooders/repos/letta-cloud`) 執行：

```bash
# 1. 刪除 postgres 資料目錄
rm -rf ./data/postgres

# 2. 停止執行中的 postgres 容器
docker stop $(docker ps -q --filter ancestor=ankane/pgvector)

# 3. 重啟服務 (會建立全新的 postgres)
just start-services

# 4. 等候 postgres 就緒，然後套用所有遷移
cd apps/core
export LETTA_PG_URI=postgresql+pg8000://postgres:postgres@localhost:5432/letta-core
uv run alembic upgrade head

# 5. 現在生成您的新遷移
uv run alembic revision --autogenerate -m "您的遷移描述訊息"
```

這可確保遷移是針對與主分支一致的乾淨資料庫狀態生成的，避免產生來自僅限本地變更的無意義差異 (diffs)。

## 疑難排解

- **自動生成時顯示 "Target database is not up to date"**
  - 請先執行 `uv run alembic upgrade head`（搭配適當的引擎/URI）。
- **SQLite 發生關於 ALTER CONSTRAINT 的 NotImplementedError**
  - 透過設定 `LETTA_PG_URI` 切換到 Postgres 並重新執行。
- **自動生成的遷移缺少預期的變更**
  - 確保 ORM 匯入與元資料 (`Base.metadata`) 正確，且變更的模型已匯入到 Alembic 的環境上下文 (env context) 中。
- **自動生成的遷移包含非預期的刪除 (drops) 或重新命名 (renames)**
  - 檢視模型變更；考慮使用顯式操作而非依賴自動生成。重設本地 Postgres（參閱工作流程 5）以獲得乾淨的基準。

## 參考資料

- `references/migration-commands_zh_TW.md` — `uv`、Alembic 與 `just` 的標準指令。
- `references/sqlite-vs-postgres-gotchas_zh_TW.md` — 不同引擎特有的陷阱以及如何避免。
