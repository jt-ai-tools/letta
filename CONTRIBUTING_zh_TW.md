# 🚀 如何參與 Letta 貢獻

感謝您投入時間參與本專案的貢獻！這是一份指引，協助您開始貢獻之旅。

## 1. 🚀 開始行動

### 🍴 Fork 儲存庫

首先，請先 Fork 一份個人的 Letta 複本來進行實驗。您可以將其視為您專屬的實驗場。 🎪

1. 前往 GitHub 上的 Letta 儲存庫。
2. 點擊右上角的「Fork」按鈕。

### 🚀 Clone (複製) 儲存庫

現在，將您的實驗場複製到本地電腦。

```shell
git clone https://github.com/您的用戶名/letta.git
```

### 🧩 安裝依賴項目與配置環境

本專案需要安裝並執行 **PostgreSQL**。假設您已有一個正在執行的 PostgreSQL 實例，首先需要建立使用者、資料庫，並確保已安裝 `pgvector` 擴展功能。以下是當使用者與資料庫名稱均為 `letta` 且未設定密碼時的設定步驟：

#### 1. 進入 PostgreSQL Shell
開啟您的終端機（或 Windows 上的命令提示字元）並執行：
```bash
# Mac/Linux：
sudo -u postgres psql

# Windows：
psql -U postgres

```
#### 2. 執行設定指令
進入 PostgreSQL 提示字元（您會看到 `postgres=#`）後，執行以下 SQL 語句：

```sql
-- 1. 建立一個具備登入與超級使用者權限的專用角色
CREATE ROLE letta WITH LOGIN SUPERUSER PASSWORD 'letta';

-- 2. 建立資料庫並將 'letta' 指定為所有者
CREATE DATABASE letta OWNER letta;

-- 3. 將連線切換至新建立的 'letta' 資料庫
\c letta

-- 4. 啟用用於嵌入向量 (vector embeddings) 的 pgvector 擴展功能
CREATE EXTENSION IF NOT EXISTS vector;
```

設定環境變數，告知 `letta` 程式碼如何連接 PostgreSQL 資料庫：
```shell
export LETTA_PG_URI="postgresql://${POSTGRES_USER:-letta}:${POSTGRES_PASSWORD:-letta}@localhost:5432/${POSTGRES_DB:-letta}"
```

#### 安裝 uv 與依賴項目

首先，請參考[官方安裝說明](https://docs.astral.sh/uv/getting-started/installation/)安裝 uv。

安裝 uv 後，切換到 `letta` 目錄並使用 uv 安裝專案：
```shell
cd letta
eval $(uv env activate)
uv sync --all-extras
```

接著，您需要為資料庫準備初始內容。您可以使用 alembic upgrade 從測試資料範本中填入初始內容。
```shell
uv run alembic upgrade head
```

#### 使用 uv 執行 Letta

現在，當您想使用 `letta` 時，可以使用 `uv run` 來執行任何 `letta` 指令：
```shell
uv run letta server
```

#### 安裝 pre-commit
我們建議安裝 pre-commit，以確保在開發過程中符合格式規範：
```
uv run pre-commit install
uv run pre-commit run --all-files
```
如果您未安裝 pre-commit，在提交 PR 之前需要執行 `uv run black .`。

## 2. 🛠️ 進行修改

### 🌟 建立分支 (Branch)

是時候發揮您的創意了。首先，為您精彩的修改建立一個新分支。 🧙‍♂️

```shell
git checkout -b feature/your-feature
```

### ✏️ 進行修改

現在，盡情揮灑您的創意吧！ 🎨

#### 處理資料庫遷移 (Database Migrations)
如果您是第一次執行 Letta，系統會自動設定您的資料庫。如果您正在更新 Letta，則可能需要執行遷移。若要執行遷移，請使用以下指令：
```shell
uv run alembic upgrade head
```

#### 建立新的資料庫遷移
如果您修改了資料庫模型 (database models)，則需要建立一個新的遷移。若要建立新遷移，請使用以下指令：
```shell
uv run alembic revision --autogenerate -m "在此輸入您的遷移描述訊息"
```

更多關於建立與執行遷移的資訊，請造訪 [Alembic 說明文件](https://alembic.sqlalchemy.org/en/latest/tutorial.html)。

## 3. ✅ 測試

在點擊「完成」之前，請確保一切運作正常。執行測試並確認現有測試不會報錯。如有需要，請建立新的測試。 🕵️

### 執行現有測試

執行測試：
```
uv run pytest -s tests
```

如果您是透過 pip 安裝，請執行：
```
pytest -s tests
```

### 建立新測試
如果您新增了重大功能修改，請在 `tests/` 目錄中新增對應的測試。

## 4. 🧩 新增依賴項目
如果您需要為 Letta 新增依賴項目，請透過 `uv add <PACKAGE_NAME>` 新增套件。這將會更新 `pyproject.toml` 與 `uv.lock` 檔案。如果該依賴項目不需要被所有使用者安裝，請務必在 `pyproject.toml` 中將其標記為選用項目 (optional)，並視需要在 `[project.optional-dependencies]` 下建立新的額外項目。

## 5. 🚀 提交修改

### 檢查格式
請執行以下指令，確保您的程式碼格式正確：
```
uv run black . -l 140
```

### 🚀 建立 Pull Request (PR)

就快完成了！是時候向世界分享您的心血了。 🌍

1. 造訪 [Letta GitHub](https://github.com/letta-ai/letta)。
2. 點擊「New Pull Request」按鈕。
3. 選擇基礎分支 (base branch) 為 `main`，並選擇比較分支 (compare branch) 為您的功能分支。
4. 設定一個吸引人的標題，並在說明欄位中描述您的修改內容。 🪄

## 6. 🔍 審核與核准 (Review and Approval)

維護者將進行審核，並可能建議一些改進方案或要求更多細節。一旦獲得核准，您的成果將正式成為 Letta 的一部分！

## 7. 📜 行為準則 (Code of Conduct)

請務必遵守專案的行為準則。

## 8. 📫 聯絡我們

需要協助或只想打個招呼嗎？我們隨時都在。您可以透過在此 GitHub 儲存庫提交 Issue，或是到我們的 [Discord 伺服器](https://discord.gg/9GEQrxmVyE)留言。

感謝您讓 Letta 變得更完美！

## 開發中 (WIP) - 🐋 Docker 開發
如果您偏好在完全隔離的容器環境中進行開發，可以使用以下指令啟動 Letta：
```shell
docker compose -f compose.yaml -f development.compose.yml up
```
這會將您的本地程式碼掛載 (volume mount) 到容器中，並在檔案修改時自動重新載入伺服器。
