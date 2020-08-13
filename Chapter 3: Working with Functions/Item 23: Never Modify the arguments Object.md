# 條款 23 永不修改 arguments 物件


- 不要修改 `arguments` 物件
- 將 argument 物件轉成真的陣列的方式請使用: `[].slice.call(arguments)`

### 什麼是類陣列(Array-like)?

擁有 length 的屬性，以及從 0 開始的索引，但是並不具有陣列應該有的方法。

```javascript=
function callMethod(obj, method) {
    var shift = [].shift;
    shift.call(arguments);
    shift.call(arguments);
    console.log(arguments);
    // obj[method]: 17[25], undefined
    return obj[method].apply(obj, arguments);
}
var obj = { add: function(x, y) {  return x + y } }
callMethod(obj, 'add', 17 ,25);
```

上述的程式碼會直接對於 `arguments` 物件進行修改，造成 `arguments` 中的參數會剩下如下內容:

```javascript=
Arguments(2)[17, 25, callee: ƒ, Symbol(Symbol.iterator): ƒ]
```

此時的 `arguments` 變數是 `arguments` 物件的 alias。

因此當存取 `obj[method]`，會是 `17[25]`。

而在嚴格模式與非嚴格模式下對於 `arguments` 命名是不是 `arguments` 物件的 alias 也有差別。

- 非嚴格模式: `arguments` 變數是 `arguments` 物件的 alias
- 嚴格模式: `arguments` 變數不是 `arguments` 物件的 alias

```javascript=
function strict(x) {
  'use strict';
  arguments[0] = 'modified';
  console.log(arguments);
  console.log(x);
  return x === arguments[0];
}

function nonstrict(x) {
  arguments[0] = 'modified';
  console.log(arguments);
  console.log(x);
  return x === arguments[0];
}

strict('unmodified');
nonstrict('unmodified');
```

### 透過 `[].slice.call(arguments)`，轉為真正的陣列

```javascript=
function callMethod(obj, method) {
  var args = [].slice.call(arguments, 2);
  return obj[method].apply(obj, args);
}
var obj = { add: function(x, y) {  return x + y } }
callMethod(obj, 'add', 17 ,25); //42
```

### ES6 之後轉換 arguments 物件為真正的陣列的方式

- `Array.from`

```javascript=
function callMethod(obj, method) {
  var args = Array.from(arguments).slice(2);
  return obj[method].apply(obj, args);
}
var obj = { add: function(x, y) {  return x + y } }
callMethod(obj, 'add', 17 ,25); //42
```

- `...` 其餘參數

```javascript=
function callMethod(obj, method, ...args) {
  return obj[method].apply(obj, args);
}
var obj = { add: function(x, y) {  return x + y } }
callMethod(obj, 'add', 17 ,25); //42
```

- `...` 展開運算子

```javascript=
function callMethod(obj, method) {
  let args = [...arguments].slice(2)
  return obj[method].apply(obj, args);
}
var obj = { add: function(x, y) {  return x + y } }
callMethod(obj, 'add', 17 ,25); //42
````