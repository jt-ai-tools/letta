# 關於
這些憑證用於建立連至 ADE 的 localhost HTTPS 連線。

## 操作說明
1. 安裝 [mkcert](https://github.com/FiloSottile/mkcert)
2. 執行 `mkcert -install`
3. 執行 Letta 並設定環境變數 `LOCAL_HTTPS=true`
4. 透過 [https://app.letta.com/development-servers/local/dashboard](https://app.letta.com/development-servers/local/dashboard) 存取應用程式
5. 點擊「Add remote server」並輸入 `https://localhost:8283` 作為 URL，除非您已為 ADE 設定密碼，否則請將密碼欄位留空。
