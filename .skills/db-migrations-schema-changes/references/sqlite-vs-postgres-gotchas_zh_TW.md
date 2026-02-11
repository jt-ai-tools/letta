# SQLite vs Postgres 注意事項 (letta-cloud 核心)

## SQLite 的限制

SQLite 對於 `ALTER TABLE` 的支援有限，特別是在約束 (constraints) 與外鍵 (foreign keys) 方面。在 Alembic 中，這通常會顯示為：

- `NotImplementedError: No support for ALTER of constraints in SQLite dialect...`

在 `apps/core` 中，當您針對 SQLite 執行包含刪除或變更外鍵或約束的遷移時，可能會遇到此錯誤。

### 處理方式

- 優先透過設定 `LETTA_PG_URI` 並使用以下指令，針對 Postgres 執行涉及結構定義 (schema) 變更的遷移：

  ```bash
  uv run alembic upgrade head
  ```

- 如果您必須支援 SQLite，請使用 Alembic 的批次模式 (batch mode) 模式，但對於本專案而言，大多數複雜的遷移應以 Postgres 為目標。

## 自動生成 (Autogenerate) 的差異

針對 SQLite 與 Postgres 執行 `alembic revision --autogenerate` 可能會產生不同的差異內容 (diffs)（特別是在索引與約束方面）。

建議事項：

- 對於生產環境或實際的遷移，請務必針對 Postgres 進行自動生成。
- 如果您看到大量非預期的索引刪除/新增，請確認遷移正在檢查哪個引擎，並切換至 Postgres 重新執行。

## 引擎選擇提醒

- 引擎受 `letta.settings.DatabaseChoice` 與環境變數（特別是 `LETTA_PG_URI`）控制。
- `just ready` 也會執行遷移；在依賴其資料庫步驟之前，請確保已設定您想要的引擎。
