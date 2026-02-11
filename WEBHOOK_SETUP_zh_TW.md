# 步驟完成 Webhook 設定 (Step Completion Webhook)

此功能讓您可以在 Letta 代理人迴圈 (agent loop) 中的每個步驟完成時，收到 Webhook 通知。

## 架構 (Architecture)

Webhook 服務透過以下兩種方式與 Letta 的執行架構整合：

### 1. 使用 Temporal (建議方式)

當使用 Temporal 處理代理人工作流時，Webhook 呼叫會被封裝為 Temporal 活動 (activities)，提供以下特性：
- 內建重試邏輯，且可自訂逾時時間
- 在 Temporal UI 中具備完整的可觀察性 (observability)
- 持久性 (durability) 保證
- 一致的錯誤處理
- 活動歷史記錄與重放 (replay) 能力

Webhooks 會在 Temporal 工作流中的 `create_step` 活動完成後觸發。

### 2. 不使用 Temporal (直接執行)

對於直接執行代理人（非 Temporal）的情況，Webhook 會直接從 `StepManager` 服務方法中呼叫：
- `update_step_success_async()` - 當步驟成功完成時
- `update_step_error_async()` - 當步驟因錯誤失敗時
- `update_step_cancelled_async()` - 當步驟被取消時

Webhook 會在步驟狀態提交 (commit) 到資料庫後發送。

### 共同行為

在**上述兩種**情況下：
- ✅ Webhook 失敗不會阻止步驟完成
- ✅ 步驟一律會先在資料庫中標記為已完成
- ✅ Webhook 傳送過程會記錄日誌以供偵錯
- ✅ 使用相同的驗證與 Payload 格式

## 設定 (Configuration)

設定以下環境變數以啟用 Webhook 通知：

### 必要設定

- **`STEP_COMPLETE_WEBHOOK`**: 步驟完成時接收 POST 請求的 URL 端點。
  - 範例：`https://your-app.com/api/webhooks/step-complete`

### 選填設定

- **`STEP_COMPLETE_KEY`**: 用於驗證的秘密金鑰 (secret key)。
  - 設定後，Webhook 服務會在 `Authorization` 標頭中包含此金鑰，格式為 `Bearer {key}`
  - 範例：`your-secret-webhook-key-12345`

## Webhook Payload

當步驟完成時，Webhook 服務將發送包含以下 JSON Payload 的 POST 請求：

```json
{
  "step_id": "step-01234567-89ab-cdef-0123-456789abcdef"
}
```

## 驗證 (Authentication)

如果配置了 `STEP_COMPLETE_KEY`，請求將包含一個 Authorization 標頭：

```
Authorization: Bearer your-secret-webhook-key-12345
```

您的 Webhook 端點應驗證此金鑰，以確保請求來自您的 Letta 實例。

## Webhook 端點範例

以下是一個簡單的 Webhook 端點範例（使用 FastAPI）：

```python
from fastapi import FastAPI, Header, HTTPException
from pydantic import BaseModel
import os

app = FastAPI()

class StepCompletePayload(BaseModel):
    step_id: str

WEBHOOK_SECRET = os.getenv("STEP_COMPLETE_KEY")

@app.post("/api/webhooks/step-complete")
async def handle_step_complete(
    payload: StepCompletePayload,
    authorization: str = Header(None)
):
    # 驗證 Webhook 金鑰
    if WEBHOOK_SECRET:
        if not authorization or not authorization.startswith("Bearer "):
            raise HTTPException(status_code=401, detail="Missing authorization")

        token = authorization.replace("Bearer ", "")
        if token != WEBHOOK_SECRET:
            raise HTTPException(status_code=401, detail="Invalid authorization")

    # 處理步驟完成邏輯
    print(f"步驟已完成: {payload.step_id}")

    # 您現在可以：
    # - 記錄步驟完成情況
    # - 觸發下游流程
    # - 更新應用程式狀態
    # - 發送通知

    return {"status": "success"}
```

## 使用範例

```bash
# 設定環境變數
export STEP_COMPLETE_WEBHOOK="https://your-app.com/api/webhooks/step-complete"
export STEP_COMPLETE_KEY="your-secret-webhook-key-12345"

# 啟動 Letta 伺服器
python -m letta.server
```

## 觸發 Webhook 的時機

當步驟達到終端狀態 (terminal state) 時會觸發 Webhook：

1. **成功 (Success)** - 步驟成功完成 (`StepStatus.SUCCESS`)
2. **錯誤 (Error)** - 步驟因錯誤失敗 (`StepStatus.FAILED`)
3. **取消 (Cancelled)** - 步驟被取消 (`StepStatus.CANCELLED`)

這三種狀態都會觸發 Webhook，並傳送僅包含 `step_id` 的相同 Payload。

## 行為特性

- **未設定 Webhook URL**：服務將跳過發送通知（記錄在 debug 層級）
- **Webhook 呼叫成功**：返回狀態碼 200-299，記錄在 info 層級
- **Webhook 逾時**：返回錯誤，記錄在 warning 層級（不會導致步驟失敗）
- **HTTP 錯誤**：返回非 2xx 狀態碼，記錄在 warning 層級（不會導致步驟失敗）
- **其他錯誤**：記錄在 error 層級（不會導致步驟失敗）

**重要提示**：Webhook 失敗不會阻止步驟完成。無論 Webhook 傳送狀態如何，步驟都會在資料庫中被標記為已完成。這確保了系統的可靠性——即使您的 Webhook 端點故障，也不會阻礙代理人的執行。

## 測試

若要測試 Webhook 功能：

1. 設定一個 Webhook 端點（您可以使用 [webhook.site](https://webhook.site) 進行測試）
2. 配置環境變數
3. 執行代理人，並觀察步驟完成時的 Webhook 呼叫情況

```bash
# 使用 webhook.site 的範例
export STEP_COMPLETE_WEBHOOK="https://webhook.site/your-unique-url"
export STEP_COMPLETE_KEY="test-key-123"

# 執行測試
python -m pytest apps/core/letta/services/webhook_service_test.py -v
```

## 實作細節

Webhook 通知會在以下操作後發送：
1. 步驟已持久化 (persisted) 到資料庫
2. 步驟指標 (metrics) 已記錄

這確保了在通知外部系統之前，步驟資料已完全提交。

### Temporal 整合

使用 Temporal 時，Webhook 呼叫作為一個獨立的活動 (`send_step_complete_webhook`) 執行，設定如下：

- **啟動至關閉逾時 (Start-to-close timeout)**：15 秒
- **排程至關閉逾時 (Schedule-to-close timeout)**：30 秒
- **重試行為**：封裝在 try-catch 中，防止 Workflow 因 Webhook 錯誤而失敗

這讓您可以在 Temporal UI 中監控 Webhook 的傳送情況，並獲得任何失敗的詳細資訊。

### 檔案位置

**核心服務：**
- `apps/core/letta/services/webhook_service.py` - 用於傳送 Webhook 的 HTTP 客戶端

**Temporal 整合：**
- `apps/core/letta/agents/temporal/activities/send_webhook.py` - Temporal 活動封裝
- `apps/core/letta/agents/temporal/temporal_agent_workflow.py` - 工作流整合
- `apps/core/letta/agents/temporal/constants.py` - 逾時常數

**非 Temporal 整合：**
- `apps/core/letta/services/step_manager.py` - 在 update_step_* 方法中直接呼叫

**測試：**
- `apps/core/letta/services/webhook_service_test.py` - 單元測試
