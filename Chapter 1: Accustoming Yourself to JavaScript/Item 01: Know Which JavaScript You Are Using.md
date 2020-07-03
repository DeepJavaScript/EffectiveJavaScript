# 條款 01 - 知道你所用的是哪個 JavaScript

## JavaScript 發展歷史脈絡

avaScript 語言的標準為 ECMAScript，至今(2020 年 6 月)為止有11個ECMA-262版本發表 [wiki](https://zh.wikipedia.org/wiki/ECMAScript)

|版本|發表日期|與前版本的差異|
|-|-|-|
|1|1997年6月|首版|
|2|1998年6月|格式修正，以使得其形式與ISO/IEC16262國際標準一致|
|3|1999年12月|強大的正規表示式，更好的詞法作用域鏈處理，新的控制指令，例外處理，錯誤定義更加明確，資料輸出的格式化及其它改變|
|4|放棄|由於關於語言的複雜性出現分歧，第4版本被放棄，其中的部分成為了第5版本及Harmony的基礎。|
|5|2009年12月|新增「嚴格模式（strict mode）」，一個子集用作提供更徹底的錯誤檢查，以避免結構出錯。澄清了許多第3版本的模糊規範，並適應了與規範不一致的真實世界實現的行為。增加了部分新功能，如getters及setters，支援JSON以及在物件屬性上更完整的反射。
|6|2015年6月|ECMAScript 2015（ES2015），第 6 版，最早被稱作是 ECMAScript 6（ES6），添加了類和模組的語法，其他特性包括疊代器，Python 風格的生成器和生成器表達式，箭頭函式，二進位資料，靜態型別陣列，集合（maps，sets 和 weak maps），promise，reflection 和 proxies。作為最早的 ECMAScript Harmony 版本，也被叫做 ES6 Harmony。|
|7|2016年6月|ECMAScript 2016（ES2016），第 7 版，多個新的概念和語言特性|
|8| 2017 年 6 月  | ECMAScript 2017（ES2017），第 8 版，多個新的概念和語言特性|
|9|2018年6月|ECMAScript 2018 （ES2018），第 9 版，包含了非同步迴圈，生成器，新的正規表示式特性和 rest/spread 語法。|
|10|2019年6月|2019 年 6 月  | ECMAScript 2019 （ES2019），第 10 版|
|11|2020年6月| 2020 年 6 月  | ECMAScript 2020 （ES2020），第 11 版 |

由於各家瀏覽器各自實作 Javascript 引擎，各家對標準的支援程度有異。例如： `const`、`use strict`。

## `const`

有一些瀏覽器也支援`const`定義變數，但是標準語言 ECMAScript 並未定義其行為，甚至每家瀏覽器環境`const` 實作行為不同。以「重新賦值」的行為來說：

- 沒支援 `const` 的舊環境會讓程式產生 `SyntaxError` 錯誤 (因不認識 `const` 關鍵字)
- 部份支援 `const` 的環境程式不會報錯 (因有實作 `const`，但把 `const` 視為 `var`，所以可重新賦值)
- 完全支援 `const` 的環境會讓程式產生 `TypeError` 錯誤

在[Can I Use](https://caniuse.com/#search=const)中可查詢各瀏覽器支援 `const` 的情況（目前支援程度為 94.44%）。
![](https://i.imgur.com/NZoeUP6.png)

建議：
- 使用 `const` 時，不要重新賦值
- 用 Babel 編譯成舊環境支援的 `var`

## `use strict`  嚴格模式

||特性|語法|相容性|
|---|---|---|---|
|strict<br>mode|嚴格限定 JS 某些<br>容易出錯的語言功能|必須寫在程式開頭<br>或函式區塊開頭|可與舊版本相容。<br>舊版本會視為字串求值表達式<br>，讀取後直接棄用|

1. 嚴格模式的語法是回溯兼容的 (backward-compatible) 
- ES5 開始有嚴格模式(strict mode)
- 沒有支援嚴格模式的環境，例如：ES3版本，"strict mode"後面的程式碼依然可以執行，會當作無害的陳述句來執行，不會報錯
- 支援的環境：使用嚴格模式執行

```javascript
function f(x) {
  "use strict";
  var arguments = []; // 出現 `SyntaxError: Unexpected eval or arguments in strict mode` 錯誤
}
```

2. 串接 JS 程式碼(script concatenation) 要注意嚴格模式
若有些檔案最上面有用嚴格模式，但某些檔案沒有時，將兩個檔案串接在一起時可能會發生奇怪的行為。

例如：A 檔案最上面有用嚴格模式，B 檔案則沒有，當串接這兩個檔案 (串接的順序不同時) 可能會有以下情形：
- A 檔案接著串接 B 檔案：全部檔案都是嚴格模式
- B 檔案接著串接 A 檔案：B 檔案部份沒有用嚴格模式，A 檔案部份則有用嚴格模式

建議：
- 使用嚴格模式時，開發時就應符合嚴格模式的規則，要不然就不要用
- 選擇嚴格模式應遵循以下規則，才不會發生部份原本沒用嚴格模式的程式碼會使用嚴格模式，而產生錯誤：
  - 全部都是嚴格模式，例如：使用ES6的 export 和 import (自動變成 strict mode)
  - 全部都是非嚴格模式
  - 否則，將檔案包在 IIFE，有用嚴格模式的檔案就不會影響到其他 IIFE 內的程式（書中甚至建議自己用 IIFE 包，而不是透過串接工具或模組系統幫你包）
```javascript
(function () {
    'use strict';
    const {strict1, strict2} = require ('./strict.module');
    strict1();
    strict2();
})();

(function () {
    const {nonstrict1, nonstrict2} = require('./nonstrict.module');
    nonstrict1();
    nonstrict2();
})();
```


## 重點整理
1. 決定你的應用程式支持 JavaScript 的哪些版本
   補充：(可於[此網站](https://kangax.github.io/compat-table/es6/)查詢各 JS 版本支援語法及目前瀏覽器支援語法，以及[此網站](https://node.green/)查詢 Node.js支援語法)
2. 注意 JavaScript 版本差異與語法支援，才能在所有的瀏覽器上表現出一致的行為
3. 確保你使用的任何 JavaScript 的特性對於應用程式要運行的所有環境都是支持的
4. 一定要在有嚴格模式檢查的環境來測試你的 strict 程式碼
5. 串接 script 時，注意各檔案的嚴格模式是否不同

