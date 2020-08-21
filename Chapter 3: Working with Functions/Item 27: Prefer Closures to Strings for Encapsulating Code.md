# 條款 27 封裝程式碼時優先選用 Closures 而非使用字串

封裝程式碼以「函式」表示優於「字串」

在變數(start,end)為全域範疇時，函式可以順利執行(eval 中出現的變數會被解讀為全域變數)，但指令很容易出問題

```javascript
function f() {
  alert("test");
}

function repeat(n, action) {
  for (var i = 0; i < n; i++) {
    eval(action);
  }
}

var start = [],
  end = [],
  timings = [];
repeat(5, "start.push(Date.now());f();end.push(Date.now())");
for (var i = 0; i < start.length; i++) {
  timings[i] = end[i] - start[i];
}
console.log(timings); // [1427, 2370, 1494, 1126, 608]
```

倘若把程式碼移至函式中，start 和 end 就不是全域變數，但執行此函式時會報錯

```javascript
function repeat(n, action) {
  for (var i = 0; i < n; i++) {
    eval(action);
  }
}

function f() {
  alert("test");
}

function benchmark() {
  var start = [],
    end = [],
    timings = [];
  repeat(100, "start.push(Date.now());f();end.push(Date.now())");
  for (var i = 0; i < start.length; i++) {
    timings[i] = end[i] - start[i];
  }
  return timings;
}

benchmark(); // Uncaught ReferenceError: start is not defined
```

比較好的方式是使用函式作為傳入參數，而非字串

```javascript
function repeat(n, action) {
  for (var i = 0; i < n; i++) {
    action();
  }
}
```

如此，執行 benchmark 函式就能安全的參考到區域變數

```javascript
function f() {
  alert("test");
}

function repeat(n, action) {
  for (var i = 0; i < n; i++) {
    action();
  }
}

function benchmark() {
  var start = [],
    end = [],
    timings = [];
  repeat(100, function () {
    start.push(Date.now());
    f();
    end.push(Date.now());
  });
  for (var i = 0; i < start.length; i++) {
    timings[i] = end[i] - start[i];
  }
  return timings;
}

benchmark();
```

結論：

1. 要使用字串作為參數傳入 eval 時，不要在字串中使用區域變數參考
2. 偏好使用會接受函式 callback 為參數的 API，而非接受字串傳入 eval 執行的 API
