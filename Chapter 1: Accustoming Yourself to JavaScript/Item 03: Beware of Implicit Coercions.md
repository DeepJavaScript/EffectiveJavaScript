# 條款 03 小心隱含的強制轉型(Implicit Coercions)

JavaScript 對於錯誤型別的容忍度，可以說是大到令人驚訝
 
```javascript
3 + true
```

多數語言會報錯。因為 `true` 和**算術運算** `+` 並不相容。
靜態型別語言，不被允許執行。
動態型別語言，能夠執行，不過某些語言會拋出例外。
JavaScript 會執行，並且產生結果 `4`

```javascript
"hello"(1)
null.x
```

提供錯的型別，會產生立即的錯誤。
但是，大多數的情況之下，JS不會指出錯誤，而是「強制轉型」成所預期的型別。

### 加法特性

加法，可以各別算文字、數字

```javascript
2 + 3 // 5
"hello" + " world"  //hello world
```

加法，可以混合運算文字和數字
JS會偏好文字，將數字轉文字。

```javascript
"2" + 3 //"23"
2 + "3" //"23"
```

加法，運算順序 `left-associative`

```javascript
1 + 2 + "3" // "33"
1 + "2" + 3 // "123" 
```

### 乘法

偏好數字 

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

### 強制轉型也有錯

`null` 運算中，默默轉成 `0`

```javascript
null + 32 // 32
null * 32 // 0
```

`undefined` 轉成特殊的浮點數 `NaN`

### NaN 測試上的困難

因為 JavaScript 是依循令人困惑的 IEEE 754 標準。要求 NaN 不等同於它自己。

```javascript
NaN === NaN // false
```

使用 `isNaN()`

> `isNaN` 應該要改成 `coercesToNaN` 才對
> 是否可以轉型成 `NaN`


如果你已知的值是一個數字，可以用`isNaN`來測試

```javascript
isNaN(NaN)                // true
isNaN("foo")              // true
isNaN(undefined)          // true
isNaN({})                 // true
isNaN({ valueOf: "foo"})  // true
```

ES5 相對準的測試方法

```javascript
NaN !== NaN                           // true
"foo" !== "foo"                       // false
undefined !== undefined               // false
{} !== {}                             // false
{valueOf: "foo"} !== {valueOf: "foo"} // false
```

ES6

```javascript
Number.isNaN(NaN)                // true
Number.isNaN("foo")              // false
Number.isNaN(undefined)          // false
Number.isNaN({})                 // false
Number.isNaN({ valueOf: "foo"})  // false
```

### 用來定義轉型的函數

- valueOf()  //成為數字
- toString() //成為字串

當物件用來表示數值，才定義 `valueOf` 
`obj.toString()` 應該要產生 `obj.valueOf()` 的字串表示值。

### 真假值

`if`, `||`, `&&`

所有的值都可以轉，只有七個值是 `false` 其它是 `true`

- Boolean: `false`
- Number: `0`, `-0`,  `NaN`
- String: `""`
- other: `null`, `undefined`

### 檢查 `undefined`

不要靠 隱含轉型成 `boolean` 

```javascript
function position(x) {
    if (!x)
        x = 320;
    return x
}
x = position(0);  // 320
x = position();   // 320
```
改成這樣，可以分辨 `0` 和 `undefined`
```javascript
function position(x) {
    if (typeof (x) === "undefined")
        x = 320;
    return x
}
x = position(0);  // 0
x = position();   // 320
```

### 條款 03 小心隱含的強制轉型（Implicit Coercions）
#### 強制轉型須知
- `+` 運算子運算特性：
    - 會視型別而異（字串串接 or 數字相加）。
    - 左結合的特性。
- `-`、`*`、`/`、`%` 將值轉為數字再運算。
- 位元運算子`~` `&` `^` `|` 與移位運算子 `<<` `>>` `>>>` 會將值轉為數字再運算。
- `null` 在運算中被轉為 0；`undefined` 被轉為 `NaN`。
- `NaN` 無法以相等運算子檢驗。

||1+2+'3'|'17'*3|'8'\|'1'|
|---|---|---|---|
|結果|'33'|51|9|false|

||null * 1|undefined * 1|`NaN` === `NaN`|
|---|---|---|---|
|結果|1|NaN|false|

#### :question: 如何檢驗值是否為 `NaN`
- 利用 `Nan` 不等於他自己的特性： `!==` 檢驗
    ```javascript
    var a = NaN;
    a !== a;   // true
    
    // 自己做一個函式來檢驗
    isreallyNaN(a);    // true
    
    function isreallyNaN(value) {
        return value !== value;
    }
    ```
- 用 `toString` 來檢驗
    ```javascript
    var a = NaN;
    a.toString() === 'NaN';   // true
    ```
- 用 `isNaN` 來檢驗（不好，也會有強制轉型的作用）
    ```javascript
    isNaN(NaN);          // true
    isNaN('foo');        // true
    isNaN(undefined);    // true
    isNaN({a: 1});       // true
    ```
- 用 ES6 的 `Number.isNaN` 來檢驗
    ```javascript
    Number.isNaN(NaN);          // true
    Number.isNaN('foo');        // false
    Number.isNaN(undefined);    // false
    Number.isNaN({a: 1});       // false
    ```
#### 物件的轉型
- `+` 運算子
    物件會被強制轉型為基本值（primitives）:
    ```javascript
    '把 Math 轉成字串' + Math; // '把 Math 轉成字串[object Math]'
    ```
- `toString` 方法
    物件被轉換成字串的過程，是透過隱含地呼叫 `toString` 方法：
    ```javascript
    '把 Math 轉成字串' + Math;
    
    // 背後的運作是
    Math.toString();  // '[object Math]'
    '把 Math 轉成字串' + '[object Math]';
    ```
- `valueof` 方法
    當物件同時含有 `toString` 跟 `valueof` 時，因為兩者都是回傳基本值，而具有重載特性的 `+` 就會遇到這兩個方法的優先問題。
    JavaScript 是盲目地優先選用 `valueof`。
    ```javascript
    var obj = {
        toString() {
            return '[object myObj]';
        },
        valueOf() {
            return 9;
        }
    }
    
    '試試看會回傳什麼：' + obj;  //'試試看會回傳什麼：9'
    ```
    > :question: 為何上述的 Math 例子會呼叫 `toString` 而不是 `valueOf`
    > 

#### truthy 與 falsy
除了 `false`、`0`、`-0`、`""`、`NaN`、`null`、`undesfined` 為 falsy，其它值皆為 truthy：
```javascript
function isFalsy(x) {
    return !x ? 'falsy' : 'truthy';
}

isFalsy(0);   // 'falsy'
```
在 falsy 值中要區別 `udefined`，可以使用 `typeof`：
```javascript
function isUndefined(x) {
    return typeof(x) === 'undefined' ? true : false;
}

isUndefined(undefined);   // true
isUndefined(null);        // false
```
> 使用 `x === undefined` 也可以。
> 

```javascript
"J" + { toString: function() { return "s"; } }; // "JS"
2 * { valueOf: function(){ return 3; } }; // 6
```

- 除了 `0, -0, "", NaN, null, undefined` 是 falsy 之外，其他都是 truthy。由於數值與字串可以有 falsy 的情況，所以透過判斷能不能轉為 truthy 這種方式作為判斷條件並不是很恰當，應該**透由判斷參數是否為 `undefined`** 或者**使用 ES6 的預設參數**則較為恰當。
```javascript=
function point(x, y) {
  if (!x) {
    x = 320;
  }
  if (!x) {
    y = 240;
  }
  return { x, y };
}

// 由於 0 可以轉為 falsy，所以會使判斷條件成立
point(0, 0); // { x: 320, y: 240 }
```

```javascript
function point(x, y) {
  if (typeof x === 'undefined') {
    x = 320;
  }
  if (typeof y === 'undefined') {
    y = 240;
  }
  return { x, y };
}
```

```javascript
function point(x = 320, y = 240) {
  return { x, y };
}
```



整理：
- 在 JS 若用錯誤的型別做奇怪的事 (比較、算術運算...等)，可能會發生預期外的事，因為被隱含的強制轉型了
- `+` 運算子經 overload，會依 arg 型別來決定要加法運算還是字串串接
- 物件藉由 `valueOf()` 來強制轉型成 number，而藉由 `toString()` 來強制轉型成 string
- 具有 `valueOf()` 的物件所實作的 `toString()` 應提供 `valueOf()` 產生的值的物件表示值
- 建議用 `typeof` 或比較的方式來找出 `undefined`，而非用 truthiness 來測試未定義的值 (即 `undefined` )

## `+` 運算子

`+` 運算子 overload 數字加法或字串串接，但會依 arg 的型別來決定要怎麼處理
- number + number：數字運算
- string + string：字串串接
- number + string：將 number 強制轉型成 string 後，進行字串串接

```javascript
2 + 3;  // 5
"hello" + " world";  // "hello world"

"2" + 3;  // "23"
2 + "3";  // "23"
```

`+` 運算子是從左到右 (即左結合，left-associative)：

```javascript
1 + 2 + "3";    // "33"
(1 + 2) + "3";  // "33"

1 + "2" + 3;    // "123"
(1 + "2") + 3;  // "123"
```

## 有時方便的強制轉型

可用來自動轉換使用者輸入、文字檔或網路 stream 的字串：

```javascript
"17" * 3;   // 51
"8" | "1";  // 9
```

## 看視奇怪的強制轉型

算術運算：
- `null`：被轉成 `0`
- `undefined`：被轉成特殊的浮點數 `NaN`

## 是否為 `NaN`

- `NaN` 不等於自己，所以不能用 `===` 判斷
  - 可用「不等於自己」這個特性來檢查 `NaN`
- 可用 `isNaN()` 判斷某 number 型別的值是否為 `NaN`
  - 但不能判斷不是 number 型別的值，因為這些值都會被轉成 number 型別

```javascript
var x = NaN;
x === NaN;   // false

// 書中提供的
function isReallyNaN(x) {
  return x !== x;
}
```

```javascript
isNaN(NaN);                 // true

isNaN("foo");               // true
isNaN(undefined);           // true
isNaN({});                  // true
isNaN({ valueOf: "foo" });  // true
```

補充：
- 在 ES6 前，只能用 `isNaN()` 判斷，但會有上面提到的雷，所以才使用 `!==` 來實作一個真正判斷是否為 `NaN` 的功能
- 在 ES6 提供 `Number.isNaN()` 就不需要用 `isNaN()` 了 (注意，兩個是不同的)

若想在不支援 `Number.isNaN()` 的環境使用它，可用 [polyfill](https://github.com/zloirock/core-js/blob/master/packages/core-js/modules/es.number.is-nan.js) (此連結為 core-js 提供的 polyfill，Babel 就是使用 core-js)：

```javascript
Number.isNaN = Number.isNaN || function (value) {     
  return value !== value;
}
```

## 物件的強制轉型

```javascript
"the Math object: " + Math;  // "the Math object: [object Math]"
"the JSON object: " + JSON;  // "the JSON object: [object JSON]"

Math.toString();  // "[object Math]"
JSON.toString();  // "[object JSON]"

"J" + { toString: () => "S" }; // "JS"
2 * { valueOf: () => 3 };      // 6
```

物件用 `+` 運算子時，會執行 `valueOf()`。若 `valueOf()` 的回傳值為 number：
- `+` 的另一個 arg 是 string 時，會將回傳值 number 轉成 string，最後進行字串串接
- `+` 的另一個 arg 是 number 時，會直接進行數字加法運算

```javascript
var obj = {
  toString: () => "[object MyObject]",
  valueOf: () => 17
};

"object: " + obj; // "object: 17"
1 + obj;          // 18
```

註：`valueOf()` 是用來表達數值的物件所設計的，例如：`Number` 物件。這些物件的 `toString()` 和 `valueOf()` 會回傳一致的結果：同一個數字的字串表示值 (string representation) 或數字表示值 (numeric representation)，這樣 overload 的 `+` 才會有一致的行為，例如：

```javascript
var n = Number(1);
n + 1;    // 2
n + '1';  // '11'
```

一般來說，強制轉型成 string 比強制轉型成 number 還少見，用處也比較小。

除非你的物件是用來表示數值的，否則最好不要有 `valueOf()`，而 `obj.toString()` 應要產生 `obj.valueOf()` 的字串表示值。

## boolean 的強制轉型 (truthiness，作為真假值)

相關的：
- `if`
- `||`、`&&` 運算子

補充：
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

所以應該改為：

```javascript
function point(x, y) {
  if (x === undefined) x = 320;
  if (y === undefined) y = 240;
  return { x, y };
}

// 或
function point(x, y) {
  if (typeof x === "undefined") x = 320;
  if (typeof y === "undefined") y = 240;
  return { x, y };
}

point();      // { x: 320, y: 240 }
point(0, 0);  // { x: 320, y: 240 }
```
