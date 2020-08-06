# 條款 15 小心 Block-Local 函式宣告造成不可移植的範疇

內容完全過時了。

1. function name 也擁有 hoisting
2. "use strict"; 以 block-scope 為主

## 巢狀函式宣告(Nested function declaration)

函式中可以宣告其他函式，這個函式又稱為內部函式(inner function)，因而形成巢狀函式(nested function)的結構。

```javascript
function f() {
  return "global";
}

function test(x) {
  function f() {
    return "local";
  }

  var result = [];
  if (x) {
    result.push(f());
  }
  result.push(f());
  return result;
}

test(true); // ["local", "local"]
test(false); // ["local"]
```

倘若函式宣告位於區域區塊(local block)中...

```javascript
function f() {
  return "global";
}

function test(x) {
  var result = [];
  if (x) {
    //block-local
    function f() {
      return "local";
    }
    result.push(f());
  }
  result.push(f());
  return result;
}

test(true); // ["local", "local"]
test(false); // Uncaught TypeError: f is not a function
```

由於內部函式 f 的語彙範疇為 test 函式，f 一開始會 hoisting 至 test 函式最上層，當 x 為 false，result.push(f())的 f 為 undefined，出現 TypeError。

倘若將函式設定為嚴格模式(function strict mode)，函式語彙範疇以 block-scope 為主

```javascript
function f() {
  return "global";
}

function test(x) {
  "use strict";
  var result = [];
  if (x) {
    function f() {
      return "local";
    }
    result.push(f());
  }
  result.push(f());
  return result;
}

test(true); // ["local", "global"]
test(false); // ["local"]
```

建議使用變數配合條件式的指定

```javascript
function f() {
  return "global";
}

function test(x) {
  var g = f;
  var result = [];
  if (x) {
    g = function f() {
      return "local";
    };
    result.push(g());
  }
  result.push(g());
  return result;
}

test(true); // ["local", "local"]
test(false); // ["global"]
```

## 結論

1. 總是把函式宣告放在一個程式或包含它的函式的最外層
2. 使用 var 宣告配合條件式的指定來取代條件式的函式宣告
