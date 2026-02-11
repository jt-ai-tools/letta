### 外掛程式 (Plugins)

外掛程式讓各種組件能夠實現「隨插即用」。

外掛程式的設定可以在 `letta.settings.settings` 中進行設定。

外掛程式設定接收一個由各個外掛程式設定組成的分隔列表 (delimited list)：

`<plugin_name>.<config_name>=<class_or_function>`

並以 `;` 符號連接。

在預設設定中，第一層的鍵 (keys) 為 `plugin_name`，其下方嵌套了 `config_name`，而 `class_or_function` 則定義在後方，格式為 `<module_path>:<name>`。

```
DEFAULT_PLUGINS = {
    "experimental_check": {
        "default": "letta.plugins.defaults:is_experimental_enabled",
        ...
```
