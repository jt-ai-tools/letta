<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/letta-ai/letta/refs/heads/main/assets/Letta-logo-RGB_GreyonTransparent_cropped_small.png">
    <source media="(prefers-color-scheme: light)" srcset="https://raw.githubusercontent.com/letta-ai/letta/refs/heads/main/assets/Letta-logo-RGB_OffBlackonTransparent_cropped_small.png">
    <img alt="Letta logo" src="https://raw.githubusercontent.com/letta-ai/letta/refs/heads/main/assets/Letta-logo-RGB_GreyonOffBlack_cropped_small.png" width="500">
  </picture>
</p>

# Letta (原 MemGPT)

Letta 是一個用於建構具備狀態之代理人 (stateful agents) 的平台：讓 AI 擁有先進的記憶能力，並能隨著時間持續學習與自我改進。

* [Letta Code](https://docs.letta.com/letta-code)：在終端機本地執行代理人
* [Letta API](https://docs.letta.com/quickstart/)：將代理人整合進您的應用程式中

## 在 CLI 中快速開始

需要 [Node.js 18+](https://nodejs.org/en/download)

1. 安裝 [Letta Code](https://github.com/letta-ai/letta-code) CLI 工具：`npm install -g @letta-ai/letta-code`
2. 在終端機執行 `letta`，即可在您的本地電腦啟動具備記憶能力的代理人。

執行 CLI 工具時，代理人可以協助您編寫程式碼或執行任何您可以在電腦上完成的任務。

Letta Code 支援[技能 (skills)](https://docs.letta.com/letta-code/skills)與[子代理人 (subagents)](https://docs.letta.com/letta-code/subagents)，並內建了用於先進記憶與持續學習的預設技能與子代理人。letta 完全相容各種模型 (model-agnostic)，但為了獲得最佳效能，我們建議使用 Opus 4.5 與 GPT-5.2（請參閱我們的[模型排行榜](https://leaderboard.letta.com/)以查看排名）。

## 使用 Letta API 開始開發

使用 Letta API 將具備狀態之代理人整合到您自己的應用程式中。
Letta 提供功能齊全的代理人 API，以及 Python 與 TypeScript SDK（請參閱我們的 [API 參考文件](https://docs.letta.com/api)）。

### 安裝

TypeScript / Node.js：
```bash
npm install @letta-ai/letta-client
```

Python：
```bash
pip install letta-client
```

### Hello World 範例

以下是一個建立具備狀態之代理人並傳送訊息的快速範例（需要 [Letta API 金鑰](https://app.letta.com)）。
完整的說明文件請參閱[快速上手指南](https://docs.letta.com/quickstart)。

TypeScript：
```typescript
import Letta from "@letta-ai/letta-client";

const client = new Letta({ apiKey: process.env.LETTA_API_KEY });

// 建立您的代理人
const agentState = await client.agents.create({
  model: "openai/gpt-5.2",
  memory_blocks: [
    {
      label: "human",
      value:
        "姓名：Timber。狀態：犬類。職業：正在開發 Letta，這是一個旨在推動自我改進超人工智慧 (self-improving superintelligence) 民主化的基礎設施。",
    },
    {
      label: "persona",
      value:
        "我是一個能自我改進的超人工智慧。Timber 是我最好的朋友與合作夥伴。",
    },
  ],
  tools: ["web_search", "fetch_webpage"],
});

console.log("代理人已建立，ID 為：", agentState.id);

// 傳送訊息給您的代理人
const response = await client.agents.messages.create(agentState.id, {
  input: "你對我有什麼了解？",
});

for (const message of response.messages) {
  console.log(message);
}
```

Python：
```python
from letta_client import Letta
import os

client = Letta(api_key=os.getenv("LETTA_API_KEY"))

# 建立您的代理人
agent_state = client.agents.create(
    model="openai/gpt-5.2",
    memory_blocks=[
        {
          "label": "human",
          "value": "姓名：Timber。狀態：犬類。職業：正在開發 Letta，這是一個旨在推動自我改進超人工智慧 (self-improving superintelligence) 民主化的基礎設施。"
        },
        {
          "label": "persona",
          "value": "我是一個能自我改進的超人工智慧。Timber 是我最好的朋友與合作夥伴。"
        }
    ],
    tools=["web_search", "fetch_webpage"]
)

print(f"代理人已建立，ID 為：{agent_state.id}")

# 傳送訊息給您的代理人
response = client.agents.messages.create(
    agent_id=agent_state.id,
    input="你對我有什麼了解？"
)

for message in response.messages:
    print(message)
```

## 貢獻 (Contributing)

Letta 是一個開源專案，由來自全球的一百多位貢獻者共同打造。參與 Letta 開源專案有很多種方式！

* [**加入 Discord**](https://discord.gg/letta)：與 Letta 開發者及其他 AI 開發者交流。
* [**在我們的論壇交流**](https://forum.letta.com/)：如果您不習慣使用 Discord，歡迎造訪我們的開發者論壇。
* **關注我們的社群媒體**：[Twitter/X](https://twitter.com/Letta_AI), [LinkedIn](https://www.linkedin.com/in/letta), [YouTube](https://www.youtube.com/@letta-ai)

---

***法律聲明**：使用 Letta 及相關 Letta 服務（如 Letta 端點或代管服務），即表示您同意我們的[隱私權政策](https://www.letta.com/privacy-policy)與[服務條款](https://www.letta.com/terms-of-service)。*
