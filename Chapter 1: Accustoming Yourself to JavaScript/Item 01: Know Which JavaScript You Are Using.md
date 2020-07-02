# 條款 01 知道你所用的是哪個 JavaScript

至今為止有七個ECMA-262版本發表 [~wiki~](https://zh.wikipedia.org/wiki/ECMAScript)

|版本|發表日期|與前版本的差異|
|-|-|-|
|1|1997年6月|首版|
|2|1998年6月|格式修正，以使得其形式與ISO/IEC16262國際標準一致|
|3|1999年12月|強大的正規表示式，更好的詞法作用域鏈處理，新的控制指令，例外處理，錯誤定義更加明確，資料輸出的格式化及其它改變|
|4|放棄|由於關於語言的複雜性出現分歧,第4版本被放棄,其中的部分成為了第5版本及Harmony的基礎。|
|5|2009年12月|新增「嚴格模式（strict mode）」，一個子集用作提供更徹底的錯誤檢查,以避免結構出錯。澄清了許多第3版本的模糊規範,and |accommodates behaviour of real-world implementations that differed consistently from that specification。增加了部分新功能,如getters及setters,支援JSON以及在物件屬性上更完整的反射。[5][6][7][8]
|6|2015年6月|多個新的概念和語言特性。ECMAScript Harmony將會以「ECMAScript 6」發布。|
|7|2016年6月|多個新的概念和語言特性|

小心版本差異與標準版，才能在所有的瀏覽器上表現出一致的行為

```javascript
const pi = 3.14;
```
ES5 不支援, ES6支援

```javascript
function f() {
    "use strict";
    var arguments = []; //Uncaught SyntaxError: Unexpected eval or arguments in strict mode 
}
```

ES5 支援, 設計成這樣是為了向上相容性，在舊版沒有副作用。

> 使用`"use strict"` 要注意 script concatenation (指令稿串接)
 
解決方法: 
- 永不串接 strict 檔案與 nonstrict 檔案。
- 將它們包在 `function` 內 (不同的`scop`)


### 條款 01 知道你所用的是哪個 JavaScript
#### JavaScript 脈絡
- 該語言的標準為 ECMAScript，由各家瀏覽器實作 Javascript 引擎。各家對標準的支援程度有異：
  如 `const`。（本書出版的 2013 年支援程度有異）
    > 目前支援程度為 94.44%
    > >
    ![](https://i.imgur.com/NZoeUP6.png)
- 主要版本演進：1999 年的 ES3 -> 2009 年的 ES5
- ES5 的 strict mode（嚴格模式）

#### ES5 的 strict mode
||特性|語法|相容性|
|---|---|---|---|
|strict<br>mode|嚴格限定 JS 某些<br>容易出錯的語言功能|必須寫在程式開頭<br>或函式區塊開頭|可與舊版本相容。<br>舊版本會視為字串求值表達式<br>，讀取後直接棄用|
##### :smiley_cat: 語法範例：
```javascript
function foo () {
    'use strict';
    var arguments = []; //error: 重新定義了 arguments
}
```
##### :question: 如何在同專案整合 strict 與 nonstrict 檔案：
分開用即刻調用函式（IIFE）包裹
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

-  考量程式碼兼容性，最好的方式是將程式碼封裝並於每一個封裝的函式中設定嚴格模式(模組化管理)。
  -  IIFE + strict mode
  -  export, import(內建 strict mode)

- 從ES5開始，新增了嚴格模式（strict mode）。
- 嚴格模式支持向上兼容，因此，ES5以前的版本(不支援嚴格模式)"strict mode"後面的程式碼依然可以執行。
- "strict mode"指令必須被放在一段程式碼的最上方，或者一個函式的頂端，才有作用。

以上這些規則會在合併JS檔案時造成一些問題，假如有些JS程式碼是嚴格模式，有些不是，合併或許會導致一些問題，所以盡量不要合併嚴格模式的JS檔案和非嚴格模式的JS檔案。

解決方法：用IIFE包住每個JS檔案的內容
```javascript
// no strict-mode directive
(function() {
	// file1.js
	"use strict";
	function f() {
		// ...
	}
	// ...
})();
(function() {
	// file2.js
	// no strict-mode
	function f() {
		var arguments = [];
		// ...
	}
	// ...
```


重點
1. 決定你的應用程式支持 JavaScript 的哪些版本。
2. 確保你使用的任何 JavaScript 的特性對於應用程式要運行的所有環境都是支持的。
3. 總是在執行嚴格模式檢查的環境中測試嚴謹的程式碼
4. 注意在合併JS程式碼時是否在同樣嚴格模式底下

整理：
- 決定你的 app 要支援哪個版本的 JS
- 確定你用的任何 JS 功能在 app 執行的所有環境都有支援
- 一定要在有嚴格模式檢查的環境來測試你的 strict 程式碼
- 串接 script 時，注意各檔案的嚴格模式是否不同

## `const`

- 沒支援 `const` 的舊環境會讓程式產生 `SyntaxError` 錯誤 (因不認識 `const` 關鍵字)
- 部份支援 `const` 的環境程式不會報錯 (因有實作 `const`，但把 `const` 視為 `var`，所以可重新賦值)
- 完全支援 `const` 的環境會讓程式產生 `TypeError` 錯誤

建議：
- 使用 `const` 時，不要重新賦值
- 用 Babel 編譯成舊環境支援的 `var`

## `'use strict';` 嚴格模式

嚴格模式的語法是 backward-compatible (回溯相容的)：
- 沒有實作嚴格模式的環境也可執行：當作無害的陳述句來執行，求出此字，然後馬上把這個值丟棄
- 支援的環境：使用嚴格模式執行

像下面範例：
- 不支援的環境：照常執行，不會出錯
- 支援的環境：會發生 `SyntaxError: Unexpected eval or arguments in strict mode` 錯誤

```javascript
function f(x) {
  "use strict";
  var arguments = [];
  // ...
}
```

若有些檔案最上面有用嚴格模式，但某些檔案沒有時，當將兩個檔案串接在一起時可能會發生奇怪的行為。

例如：A 檔案最上面有用嚴格模式，B 檔案則沒有，當串接這兩個檔案 (串接的順序不同時) 可能會有以下可能：
- A 檔案接著串接 B 檔案：全部檔案都是嚴格模式
- B 檔案接著串接 A 檔案：B 檔案部份沒有用嚴格模式，A 檔案部份則有用嚴格模式

建議：
- 使用嚴格模式時，開發時就應符合嚴格模式的規則，要不然就不要用
- 選擇嚴格模式應遵循以下規則，才不會發生部份原本沒用嚴格模式的程式碼會使用嚴格模式，而產生錯誤：
  - 全部都是嚴格模式
  - 全部都是非嚴格模式
  - 否則將各檔案包在 IIFE，有用嚴格模式的檔案就不會影響到其他 IIFE 內的程式
    - 書中甚至建議自己用 IIFE 包，而不是透過串接工具或模組系統幫你包
    - 但我建議應習慣嚴格模式，而不是害怕它而用 IIFE 避開它
