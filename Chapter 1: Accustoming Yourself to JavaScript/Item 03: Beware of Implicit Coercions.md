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