---
__廣告 :)__

- __[pica](https://nodeca.github.io/pica/demo/)__ - 在瀏覽器中進行高品質且快速的圖片縮放。
- __[babelfish](https://github.com/nodeca/babelfish/)__ - 對開發者友善、支援複數形式且語法簡單的 i18n。

您會喜歡這些專案的！

---

# h1 標題 8-)
## h2 標題
### h3 標題
#### h4 標題
##### h5 標題
###### h6 標題


## 水平線

___

---

***


## 排版替換

啟用排版選項以查看結果。

(c) (C) (r) (R) (tm) (TM) (p) (P) +-

測試.. 測試... 測試..... 測試?..... 測試!....

!!!!!! ???? ,,  -- ---

"智慧引號，雙引號" 與 '單引號'


## 強調

**這是粗體文字**

__這是粗體文字__

*這是斜體文字*

_這是斜體文字_

~~刪除線~~


## 區塊引用 (Blockquotes)


> 區塊引用也可以嵌套...
>> ...透過在旁邊使用額外的大於符號...
> > > ...或者在箭頭之間使用空格。


## 列表

無序列表

+ 在行首使用 `+`、`-` 或 `*` 來建立列表
+ 子列表透過縮排 2 個空格來建立：
  - 標記字元的改變會強制開始新列表：
    * 第一項
    + 第二項
    - 第三項
+ 非常簡單！

有序列表

1. 第一項內容
2. 第二項內容
3. 第三項內容


1. 您可以使用連續的數字...
1. ...或者全部保持為 `1.`

從特定的位移開始編號：

57. foo
1. bar


## 程式碼

行內 `程式碼`

縮排程式碼

    // 一些註解
    程式碼第 1 行
    程式碼第 2 行
    程式碼第 3 行


區塊程式碼 "fences"

```
此處為範例文字...
```

語法突顯

``` js
var foo = function (bar) {
  return bar++;
};

console.log(foo(5));
```

## 表格

| 選項 | 描述 |
| ------ | ----------- |
| data   | 資料檔案路徑，用於提供傳遞給範本的資料。 |
| engine | 用於處理範本的引擎。預設為 Handlebars。 |
| ext    | 用於目標檔案的副檔名。 |

靠右對齊欄位

| 選項 | 描述 |
| ------:| -----------:|
| data   | 資料檔案路徑，用於提供傳遞給範本的資料。 |
| engine | 用於處理範本的引擎。預設為 Handlebars。 |
| ext    | 用於目標檔案的副檔名。 |


## 連結

[連結文字](http://dev.nodeca.com)

[帶有標題的連結](http://nodeca.github.io/pica/demo/ "標題文字！")

自動轉換的連結 https://github.com/nodeca/pica (需啟用 linkify)


## 圖片

![Minion](https://octodex.github.com/images/minion.png)
![Stormtroopocat](https://octodex.github.com/images/stormtroopocat.jpg "The Stormtroopocat")

與連結類似，圖片也有腳註樣式的語法

![替代文字][id]

在文件後方定義 URL 位置：

[id]: https://octodex.github.com/images/dojocat.jpg  "The Dojocat"


## 外掛程式 (Plugins)

`markdown-it` 的殺手級功能是對
[語法外掛程式](https://www.npmjs.org/browse/keyword/markdown-it-plugin) 的高效支援。


### [表情符號 (Emojies)](https://github.com/markdown-it/markdown-it-emoji)

> 傳統標記：:wink: :cry: :laughing: :yum:
>
> 快捷鍵 (顏文字)：:-) :-( 8-) ;)

請參閱[如何變更輸出](https://github.com/markdown-it/markdown-it-emoji#change-output)與 twemoji。


### [下標 (Subscript)](https://github.com/markdown-it/markdown-it-sub) / [上標 (Superscript)](https://github.com/markdown-it/markdown-it-sup)

- 19^th^
- H~2~O


### [\<ins>](https://github.com/markdown-it/markdown-it-ins)

++插入的文字++


### [\<mark>](https://github.com/markdown-it/markdown-it-mark)

==標記的文字==


### [腳註 (Footnotes)](https://github.com/markdown-it/markdown-it-footnote)

腳註 1 連結[^first]。

腳註 2 連結[^second]。

行內腳註^[行內腳註的文字]定義。

重複的腳註參考[^second]。

[^first]: 腳註**可以包含標記**

    以及多個段落。

[^second]: 腳註文字。


### [定義列表 (Definition lists)](https://github.com/markdown-it/markdown-it-deflist)

術語 1

:   定義 1
帶有延後續行。

帶有 *行內標記* 的術語 2

:   定義 2

        { 程式碼，定義 2 的一部分 }

    定義 2 的第三段。

_緊湊樣式：_

術語 1
  ~ 定義 1

術語 2
  ~ 定義 2a
  ~ 定義 2b


### [縮寫 (Abbreviations)](https://github.com/markdown-it/markdown-it-abbr)

這是 HTML 縮寫範例。

它會轉換 "HTML"，但保留像 "xxxHTMLyyy" 這樣的部分內容。

*[HTML]: 超文件標示語言 (Hyper Text Markup Language)

### [自訂容器 (Custom containers)](https://github.com/markdown-it/markdown-it-container)

::: warning
*此處有龍 (注意危險)*
:::
