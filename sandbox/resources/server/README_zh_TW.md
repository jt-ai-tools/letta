# TS 伺服器

這是一個基礎的 TypeScript 應用程式，用於支援使用者定義的工具呼叫 (tool call) 函數。此應用程式在 Modal 容器內執行。

## 總覽 (Overview)

- `server.ts` - 監聽 unix socket 的 Node 程序 (node process)
- `entrypoint.ts` - 輕量化函數，負責將 JSON 編碼的輸入字串反序列化，並將其作為輸入傳遞給使用者定義的函數
- `user-function.ts` - 完全由使用者定義

## 操作說明

1. `npm install`
2. `npm run build`
3. 執行 `npm run start` 以啟動伺服器
