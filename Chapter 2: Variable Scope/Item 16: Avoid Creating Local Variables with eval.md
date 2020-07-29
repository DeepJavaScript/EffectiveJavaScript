# 條款 16 避免使用 eval 來建立區域變數

## 重點

`eval()` 會修改包含它的 scope，避免使用，不要依據程式動態行為來決定 scope。

- 避免用 `eval()` 建立會污染 caller 的 scope 的變數
- 若 `eval()` 的程式碼會建立全域變數，就把它包在一個巢狀函數中，以避免 scope 被污染

## 在 caller 的 local scope 中執行

- 呼叫 `eval()` 會把 argument 當作 JS 程式來解譯 (interpret)，但程式是在 caller 的 local scope 中執行
  - 下面範例的 `var` 只會在 `eval()` 被呼叫時才會被執行
- embedded 程式的全域變數會被建立為 caller 程式的區域值 (locals)

```javascript
function test(x) {
  eval("var y = x"); // 動態 binding
  return y;
}
console.log(test('hi'));  // "hi"
console.log(window.y);    // undefined
```

## 外部 caller 變更函數內部 scope

若傳給 `eval()` 的原始碼不是在 local 定義的。例如，賦予外部 caller 變更函數內部 scope 的能力：

```javascript
var y = "global";

function test(src) {
  eval(src); // 可能會動態 binding
  return y;
}
console.log(test("var y = 'local'"));  // "local"
console.log(test("var z = 'local'"));  // "global"
```

## 嚴格模式

ES5 的 strict 模式會在巢狀 scope 中執行 `eval()` 以避免污染：

```javascript
'use strict'

var y = "global";

function test(src) {
  eval(src); // 可能會動態 binding
  return y;
}
console.log(test("var y = 'local'"));  // "global"
console.log(test("var z = 'local'"));  // "global"
```

## 明確的 scope

若要確保 `eval()` 不會影響外層的 scope，可用明確的巢狀 scope 中執行 (例如：IIFE)：

```javascript
var y = "global";

function test(src) {
  (function() { eval(src); })();
  return y;
}
console.log(test("var y = 'local'"));  // "global"
console.log(test("var z = 'local'"));  // "global"
```
