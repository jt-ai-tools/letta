# Gemini 使用量統計 (Gemini Usage Statistics)

## 回應格式

Gemini 在 `usage_metadata` 中回傳使用量資訊：

```
response.usage_metadata.prompt_token_count           # 總輸入 Token 數
response.usage_metadata.candidates_token_count       # 輸出 Token 數
response.usage_metadata.total_token_count            # 總計
response.usage_metadata.cached_content_token_count   # 來自快取的 Token 數 (選填)
response.usage_metadata.thoughts_token_count         # 推理 Token 數 (選填)
```

## Token 計數方式

- `prompt_token_count` 是「總計」(包含已快取的部分)
- `cached_content_token_count` 是其子集 (如果存在)
- 此語義與 OpenAI 類似

## 隱式快取 (Implicit Caching, Gemini 2.0+)

**要求：**
- 至少需要 1,024 個 Token
- 自動啟用 (無需額外設定)
- 適用於 Gemini 2.0 Flash 及更新模型

**行為特性：**
- 快取是機率性的，且在伺服器端進行管理
- `cached_content_token_count` 欄位可能會出現，也可能不會
- 當該欄位出現時，代表從快取中讀取的 Token 數量

**注意：** 不同於 Anthropic，Gemini 不具備顯式的 `cache_control`。快取是隱式的，並由 Google 的基礎設施進行管理。

## 推理/思考 Token (Reasoning/Thinking Tokens)

對於具備延伸思考 (extended thinking) 功能的模型（例如啟用了思考功能的 Gemini 2.0）：
- `thoughts_token_count` 回報用於推理的 Token 數量
- 這些與 OpenAI 的 `reasoning_tokens` 類似

**啟用思考功能：**
```python
generation_config = {
    "thinking_config": {
        "thinking_budget": 1024  # 最大思考 Token 數
    }
}
```

## 串流 (Streaming)

在串流模式下：
- `usage_metadata` 通常出現在**最後一個區塊 (final chunk)**
- 欄位與非串流模式相同
- 中間區塊 (intermediate chunks) 可能不包含此資訊

**重要提示：** `stream_async()` 回傳的是非同步產生器 (async generator)，而非可等待對象 (awaitable)：
```python
# 正確寫法：
stream = client.stream_async(request_data, llm_config)
async for chunk in stream:
    ...

# 錯誤寫法 (會報錯)：
stream = await client.stream_async(...)  # 會產生 TypeError!
```

## API

Gemini 提供兩種 API：
- **Google AI (google_ai)：** 使用 `google.genai` SDK
- **Vertex AI (google_vertex)：** 使用相同的 SDK，但具備不同的身分驗證方式

兩者共享相同的回應格式。

## Letta 實作細節

- **客戶端 (Client)：** `letta/llm_api/google_vertex_client.py` (同時處理 google_ai 與 google_vertex)
- **串流介面 (Streaming interface)：** `letta/interfaces/gemini_streaming_interface.py`
- **擷取方法：** `GoogleVertexClient.extract_usage_statistics()`
- 回應為 `GenerateContentResponse` 物件，具備 `.usage_metadata` 屬性。
