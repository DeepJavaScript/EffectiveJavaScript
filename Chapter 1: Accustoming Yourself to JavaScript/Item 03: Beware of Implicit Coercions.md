# 條款 03 小心隱含的強制轉型 (Implicit Coercions)

JavaScript 對於錯誤型別的容忍度，可以說是大到令人驚訝。

以下方這個例子而言： 

```javascript
3 + true
```

1. 多數語言會報錯。因為 `true` 和**算術運算** `+` 並不相容。
2. 在靜態型別語言中是不被允許執行的。
3. 在動態型別語言中有些則能夠執行，不過某些語言會拋出**例外(exception)**。
4. 而 JavaScript 則會執行，並且產生結果 `4`，原因在於 **JS 能夠隱含的強制轉型成所預期的型別。**

## JS中運算子的特性

### `+` 運算子

 `+` 運算子是從左到右 (即左結合，left-associative)，且會依 arg 的型別來決定要怎麼處理:
- number + number：數字運算
- string + string：字串串接
- number + string：將 **number 強制轉型成 string 後，進行字串串接**

```javascript
2 + 3 // 5
"hello" + " world"  //hello world
// 展示 + 運算子為左結合(left-associative)的例子 
1 + 2 + "3" // "33"
1 + "2" + 3 // "123" 
```
### 乘法

偏好數字，會將字串轉為數字型別後進行運算。

```javascript
"17" * 3 //51
```

### 位元運算

包含
- 算數運算子: `~` `&` `^` `|`
- 位移運算子: `>>` `<<` `>>>`

不只是轉成數字，而是轉成「32位元整數的數字子集」

```javascript
"8" | "1" // 9
```

[9.3 ToNumber](https://www.ecma-international.org/ecma-262/5.1/#sec-9.3)

### 看視奇怪的強制轉型

在 `null` 運算中，默默轉成 `0`，而 `undefined` 轉成特殊的浮點數 `NaN`。

```javascript
null + 32 // 32
null * 32 // 0
```

### 在 NaN 測試上的困難

因為 JavaScript 是依循令人困惑的 IEEE 754 標準。**要求 NaN 不等同於它自己**。

```javascript
NaN === NaN // false
```

因此，不能透過 `===` 的方式判斷，但可以透過「不等於自己」這個特性來檢查 `NaN`，以書中提到的例子而言：

```javascript
function isReallyNaN(x) {
  return x !== x;
}
```

此外，我們也可以透過`isNaN()` 判斷某 number 型別的值是否為 `NaN`，但是需要注意的是不能判斷不是 number 型別的值，因為這些值都會被轉成 number 型別。

如果你已知的值是一個數字，可以用`isNaN`來測試

```javascript
isNaN(NaN)                // true
isNaN("foo")              // true
isNaN(undefined)          // true
isNaN({})                 // true
isNaN({ valueOf: "foo"})  // true
```

ES6 之後提供了 `Number.isNaN()` ，解決了 `isNaN()` 的不足：

```javascript
Number.isNaN(NaN)                // true
Number.isNaN("foo")              // false
Number.isNaN(undefined)          // false
Number.isNaN({})                 // false
Number.isNaN({ valueOf: "foo"})  // false
```

若想在不支援 `Number.isNaN()` 的環境使用它，可用 [polyfill](https://github.com/zloirock/core-js/blob/master/packages/core-js/modules/es.number.is-nan.js) (此連結為 core-js 提供的 polyfill，Babel 就是使用 core-js)：

```javascript
Number.isNaN = Number.isNaN || function (value) {     
  return value !== value;
}
```

### 物件的強制轉型

物件用 `+` 運算子時，會執行 `valueOf()`。若 `valueOf()` 的回傳值為 number：
- `+` 的另一個 arg 是 string 時，會將回傳值 number 轉成 string，最後進行字串串接
- `+` 的另一個 arg 是 number 時，會直接進行數字加法運算

```javascript
"the Math object: " + Math;  // "the Math object: [object Math]"
"the JSON object: " + JSON;  // "the JSON object: [object JSON]"

Math.toString();  // "[object Math]"
JSON.toString();  // "[object JSON]"

"J" + { toString: () => "S" }; // "JS"
2 * { valueOf: () => 3 };      // 6
```

再來另外看看一個例子，當物件若是定義了 `toString()`, `valueOf()` 時，執行順序會是先查找是否有 `valueOf()` 如果查找不到，才會查找 `toString()`。

```javascript
var obj = {
  toString: () => "[object MyObject]",
  valueOf: () => 17
};

"object: " + obj; // "object: 17"
1 + obj;          // 18
```

- [OrdinaryToPrimitive ( O, hint )](https://read262.netlify.app/abstract-operations/type-conversion#sec-ordinarytoprimitive)

### boolean 的強制轉型

相關的：
- `if`
- `||`、`&&` 運算子

spec 提到的 ToBoolean(argument) 會強制轉型成 `boolean` 型別：
- `undefined` 和 `null` 會轉成 `false`
- `boolean` 不會轉換
- `number` 時：
  - `+0`、`-0` 或 `NaN` 會轉成 `false`
  - 否則會轉成 `true`
- `string` 時：
  - `""` 空字串 (length 為 0) 會轉成 `false`
  - 否則會轉成 `true`
- `object` 會轉成 `true`

非 spec 的說法就是分成 truthy 和 falsy：
- falsy 的值：被強制轉型為 `boolean` 時會變成 `false` 值，例如：
  - `undefined`、`null`
  - `false`
  - `+0`、`-0`、`NaN`
  - `""`
- truthy 的值：falsy 的值之外的其他值被強制轉型為 `boolean` 時會變成 `true` 值，例如：
  - `true`
  - 除了 `+0`、`-0`、`NaN` 之外的 `number`
  - 除了 `""` 的 `string`
  - 所有 `object`

### 檢查 `undefined`

若使用 truthiness 來檢查函數的 arg 或物件 property 是否有定義時，可能會不安全。

例如：不輸入 arg 時會設定預設值，但設定 `point(0, 0)` 時，`x` 和 `y` 應為 `0`：

```javascript
function point(x, y) {
  if (!x) x = 320;
  if (!y) y = 240;
  return { x, y };
}

point();      // { x: 320, y: 240 }
point(0, 0);  // { x: 320, y: 240 }
```

改成這樣，可以分辨 `0` 和 `undefined`

```javascript
function position(x) {
  if (typeof (x) === "undefined")
    x = 320;
  return x ?? 320;
}
x = position(0);  // 0
x = position();   // 320
```

或者可以透過新的語法來處理這個問題:

```javascript
// Nullish Coalescing ??
function position(x) {
  return x ?? 320;
}
x = position(0);  // 0
x = position();   // 320
```

```javascript
// 使用 ES6 的預設參數
function pposition(x = 320) {
  return x;
}
x = position(0);  // 0
x = position();   // 320
```

- [Optional Chaining for JavaScript ?.](https://github.com/tc39/proposal-optional-chaining#optional-chaining-for-javascript)

- [Nullish Coalescing ??](https://github.com/tc39/proposal-nullish-coalescing)
