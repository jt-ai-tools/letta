---
name: 錯誤報告 (Bug report)
about: 建立報告以協助我們改進
title: ''
labels: ''
assignees: ''

---

> [!IMPORTANT]
> **🚨 欲回報 Letta Code 的錯誤？** 
> 
> 請改至 **[letta-ai/letta-code](https://github.com/letta-ai/letta-code/issues)** 提交議題！
> 
> 本儲存庫僅用於 Letta Docker 核心伺服器。與 Letta Code CLI 工具或代理人程式開發功能相關的議題，請在 Letta Code 儲存庫中回報。

---

**描述錯誤**
請簡潔且清楚地描述錯誤內容。

**請描述您的環境設定**
- [ ] 您是如何執行 Letta 的？
  - Docker
  - pip (舊版/legacy)
  - 從原始碼 (From source)
  - 桌面版
- [ ] 描述您的環境
  - 您的作業系統是什麼 (Windows/MacOS/Linux)？
  - 您的 `docker run ...` 指令為何（如有）？

**螢幕截圖**
若情況允許，請提供螢幕截圖以協助說明您的問題。

**其他背景資訊**
請在此處加入關於此問題的任何其他背景資訊。
- 您正在使用的模型

**代理人檔案 (選填)**
請附上您的 `.af` 檔案，這將有助於我們重現問題。


---

如果您不是使用 OpenAI，請提供關於您本地 LLM 設定的額外資訊：

**本地 LLM 詳細資訊**

如果您正嘗試搭配本地 LLM 執行 Letta，請提供以下資訊：

- [ ] 您正嘗試使用的確切模型（例如 `dolphin-2.1-mistral-7b.Q6_K.gguf`）
- [ ] 您正在使用的本地 LLM 後端（web UI？LM Studio？）
- [ ] 您執行本地 LLM 後端的硬體（本地電腦？作業系統？遠端 RunPod？）
