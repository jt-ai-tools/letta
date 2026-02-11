# Anthropic 使用量統計 (Anthropic Usage Statistics)

## 回應格式

```
response.usage.input_tokens                  # 僅包含「非快取」的輸入 Token
response.usage.output_tokens                 # 輸出 Token
response.usage.cache_read_input_tokens       # 從快取讀取的 Token
response.usage.cache_creation_input_tokens   # 寫入快取的 Token
```

## 重要：Token 計算方式

**Anthropic 的 `input_tokens` 並非總量。** 若要取得總輸入 Token 數：

```python
總輸入 = input_tokens + cache_read_input_tokens + cache_creation_input_tokens
```

這與 OpenAI/Gemini 不同，後者的 `prompt_tokens` 已經是總量。

## 前綴快取 (提示詞快取，Prompt Caching)

**要求：**
- Claude 3.5 Haiku/Sonnet 至少需要 1,024 個 Token
- Claude 3 Opus 至少需要 2,048 個 Token
- 需要在訊息中顯式設定 `cache_control` 中斷點 (breakpoints)
- TTL (生存時間)：5 分鐘

**如何啟用：**
在訊息內容中加入 `cache_control`：
```python
{
    "role": "user",
    "content": [
        {
            "type": "text",
            "text": "...",
            "cache_control": {"type": "ephemeral"}
        }
    ]
}
```

**需要 Beta 版標頭 (Beta header)：**
```python
betas = ["prompt-caching-2024-07-31"]
```

## 快取行為

- `cache_creation_input_tokens`：本次請求中被快取的 Token（快取寫入）
- `cache_read_input_tokens`：從現有快取中讀取的 Token（快取命中）
- 第一次請求：預期 `cache_creation_input_tokens > 0`
- 隨後具有相同前綴的請求：預期 `cache_read_input_tokens > 0`

## 串流 (Streaming)

在串流模式下，使用量會回報在兩個事件中：

1. **`message_start`**：初始使用量（可能包含快取資訊）
   ```python
   event.message.usage.input_tokens
   event.message.usage.output_tokens
   event.message.usage.cache_read_input_tokens
   event.message.usage.cache_creation_input_tokens
   ```

2. **`message_delta`**：累計輸出 Token
   ```python
   event.usage.output_tokens  # 這是「累計量」，而非「增量」
   ```

**重要提示：** 根據 Anthropic 官方文件，`message_delta` 的 Token 計數是累計的，因此應直接賦值（不要進行累加）。

## Letta 實作細節

- **客戶端 (Client)：** `letta/llm_api/anthropic_client.py`
- **串流介面 (Streaming interfaces)：**
  - `letta/interfaces/anthropic_streaming_interface.py`
  - `letta/interfaces/anthropic_parallel_tool_call_streaming_interface.py` (追蹤快取 Token)
- **擷取方法：** `AnthropicClient.extract_usage_statistics()`
- **快取控制：** `_add_cache_control_to_system_message()`, `_add_cache_control_to_messages()`
