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

- 從ES5開始，新增了嚴格模式（strict mode）。
- 嚴格模式支持向上兼容，因此，ES5以前的版本(不支援嚴格模式)"strict mode"後面的程式碼依然可以執行。
- "strict mode"指令必須被放在一段程式碼的最上方，或者一個函式的頂端，才有作用。

以上這些規則會在合併JS檔案時造成一些問題，假如有些JS程式碼是嚴格模式，有些不是，合併或許會導致一些問題，所以盡量不要合併嚴格模式的JS檔案和非嚴格模式的JS檔案。

解決方法：用IIFE包住每個JS檔案的內容
```
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
