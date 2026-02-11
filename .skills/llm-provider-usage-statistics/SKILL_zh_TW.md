---
name: llm-provider-usage-statistics
description: 跨 LLM 提供者 (OpenAI, Anthropic, Gemini) 的 Token 計數與前綴快取 (prefix caching) 參考指南。用於除錯 Token 計數或優化前綴快取時。
---

# LLM 提供者使用量統計 (LLM Provider Usage Statistics)

關於不同 LLM 提供者如何回報 Token 使用量的參考文件。

## 快速參考：Token 計數語義

| 提供者 | `input_tokens` 定義 | 快取 Token | 是否須加上快取量才等於總數？ |
|----------|------------------------|--------------|------------------------------|
| OpenAI | 總計 (包含已快取) | `cached_tokens` 是其子集 | 否 |
| Anthropic | **僅限**非快取部分 | `cache_read_input_tokens` + `cache_creation_input_tokens` | **是** |
| Gemini | 總計 (包含已快取) | `cached_content_token_count` 是其子集 | 否 |

**關鍵差異：** Anthropic 的 `input_tokens` 不包含已快取的 Token，因此您必須將它們加總：
```
總輸入 = input_tokens + cache_read_input_tokens + cache_creation_input_tokens
```

## 快速參考：前綴快取 (Prefix Caching)

| 提供者 | 最小 Token 數 | 如何啟用 | TTL (生存時間) |
|----------|-----------|---------------|-----|
| OpenAI | 1,024 | 自動 | 約 5-10 分鐘 |
| Anthropic | 1,024 | 需要 `cache_control` 中斷點 | 5 分鐘 |
| Gemini 2.0+ | 1,024 | 自動 (隱式) | 變動 |

## 快速參考：推理/思考 Token (Reasoning/Thinking Tokens)

| 提供者 | 欄位名稱 | 模型 |
|----------|-----------|--------|
| OpenAI | `reasoning_tokens` | o1, o3 系列模型 |
| Anthropic | 不適用 | (思考內容位於內容區塊，非 usage 欄位) |
| Gemini | `thoughts_token_count` | 啟用思考功能的 Gemini 2.0 |

## 提供者參考檔案

- **OpenAI:** [references/openai_zh_TW.md](references/openai_zh_TW.md) - Chat Completions 對比 Responses API、推理模型、cached_tokens
- **Anthropic:** [references/anthropic_zh_TW.md](references/anthropic_zh_TW.md) - cache_control 設定、Beta 版標頭、快取 Token 欄位
- **Gemini:** [references/gemini_zh_TW.md](references/gemini_zh_TW.md) - 隱式快取、思考 Token、usage_metadata 欄位
