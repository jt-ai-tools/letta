# OpenAI 使用量統計 (OpenAI Usage Statistics)

## API 與回應格式

OpenAI 具備兩種不同回應結構的 API：

### Chat Completions API
```
response.usage.prompt_tokens           # 總輸入 Token 數 (包含已快取部分)
response.usage.completion_tokens       # 輸出 Token 數
response.usage.total_tokens            # 總計
response.usage.prompt_tokens_details.cached_tokens        # 已快取的子集
response.usage.completion_tokens_details.reasoning_tokens # 適用於 o1/o3 模型
```

### Responses API (較新)
```
response.usage.input_tokens            # 總輸入 Token 數
response.usage.output_tokens           # 輸出 Token 數
response.usage.total_tokens            # 總計
response.usage.input_tokens_details.cached_tokens         # 已快取的子集
response.usage.output_tokens_details.reasoning_tokens     # 適用於推理模型
```

## 前綴快取 (Prefix Caching)

**要求：**
- 前綴中至少需要 1,024 個 Token
- 自動啟用 (無需額外設定)
- 以 128 個 Token 為增量單位進行快取
- TTL (生存時間)：閒置約 5-10 分鐘

**支援的模型：** GPT-4o, GPT-4o-mini, o1, o1-mini, o3-mini

**快取行為特性：**
- `cached_tokens` 將會是 128 的倍數
- 快取命中 (Cache hit) 代表這些 Token 未被重複處理
- 成本：快取 Token 的費用低於非快取 Token

## 推理模型 (o1, o3)

對於推理模型，會有額外的 Token 用於「思考」：
- `reasoning_tokens` 位於 `completion_tokens_details` 中
- 這些是用於內部推理的輸出 Token
- 不會顯示在回應內容中

## 串流 (Streaming)

在串流模式下，當 `stream_options.include_usage=True` 時，使用量會回報在**最後一個區塊 (final chunk)** 中：
```python
request_data["stream_options"] = {"include_usage": True}
```

最後一個區塊將包含 `chunk.usage`，其結構與非串流模式相同。

## Letta 實作細節

- **客戶端 (Client)：** `letta/llm_api/openai_client.py`
- **串流介面 (Streaming interface)：** `letta/interfaces/openai_streaming_interface.py`
- **擷取方法：** `OpenAIClient.extract_usage_statistics()`
- 使用 OpenAI SDK 的 Pydantic 模型 (`ChatCompletion`) 進行類型安全解析 (type-safe parsing)。
