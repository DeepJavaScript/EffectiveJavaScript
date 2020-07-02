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
:::