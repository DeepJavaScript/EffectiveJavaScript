# 條款 29 避免非標準的堆疊查閱特性

## arguments.callee & arguments.caller

在函數內部有一個特殊的對象 arguments，用以保存函數的參數。
而 arguments 還有兩個屬性供使用，分別為 callee 以及 caller

### 1. callee

callee 指向擁有這個 arguments 的函數。

階乘函數的範例：

```javascript
var factorial = function(n){
  return (n<=1)?1:(n*arguments.callee(n-1))
};
```

也可以透過函數本身的名字即可

```javascript
var factorial = function(n){
  return (n<=1)?1:(n*factorial(n-1))
};
```

❓ 但也看到另外的說法是若使用函數本身的名字就會有耦合的問題。使用 arguments.callee 可以解耦，並達到原目的。

例如：

```javascript
var factorial = function(n){
  return (n<=1)?1:(n*factorial(n-1))
};

var newFactorial = factorial
console.log(newFactorial(5)) //120

factorial = function(n){
  return 0
};
console.log(factorial(5)) //0
```

- 另外要注意的是 ES5 禁止 arguments.callee() 在嚴格模式下使用。

- 為什麼 arguments.callee() 在嚴格模式下會被禁止？ 可以參考[這邊的討論](https://stackoverflow.com/questions/103598/why-was-the-arguments-callee-caller-property-deprecated-in-javascript/235760#235760)

主要有幾個重點原因：調用 arguments 很耗性能、this 可能會改變。
關於 this 會改變，可以參考這一段：

```javascript
var global = this;
var sillyFunction = function (recursed) {
    console.log(this)
    if (!recursed)
        return arguments.callee(true);
}
sillyFunction();
// window
// Arguments
```

綜合多方討論，arguments.callee 會出現，(是因為早期版本的 JavaScript 不允許使用命名函數表達式)?。所以透過函式本身名字呼叫的寫法是不行的，這樣就無法做函數遞迴。因此出現 arguments.callee。但後來允許了也就不需要 arguments.callee 了。也因他有一些副作用，所以被 ES5 嚴格模式禁止了。

### 2. caller

因安全考量 arguments.caller 已經被移除了。
雖然有些瀏覽器仍然可以使用，但仍不建議使用了。

```javascript
function whoCalled() {
   if (arguments.caller == null)
      console.log('I was called from the global scope.');
   else
      console.log(arguments.caller + ' called me!');
}
```

取而代之的是 Function.caller，但它仍為非標準的特性！

## Function.caller

```javascript
function revealCaller() {
   return revealCaller.caller();
}
function start() {
   return revealCaller();
}
start() === start; //true
```

如果函數 f 是在全局作用域內被調用的，則 f.caller 為 null，如果一個函數 f 是在另外一個函數作用域內被調用的，則 f.caller 為 null 指向調用它的那個函數

```javascript
// 創造一個堆疊追蹤紀錄
function getCallStack() {
  var stack = [];
  for (var f = getCallStack.caller; f; f = f.caller) {
    stack.push(f);
  }
  return stack;
}

// 並試著查閱堆疊
function f1() {
  return getCallStack();
}
function f2() {
  return f1();
}
var trace = f2();
trace; // [f1, f2]
```

但如果函式在呼叫堆疊中出現超過一次，整個堆疊查閱的邏輯就會被困在一個迴圈中。
如下範例所示，函數 f 會遞迴地呼叫自己，它的 caller 特性會自動被更新指向回 f。
所以 getCallStack 中的迴圈會不斷地查看 f。呼叫堆疊的其他部分資訊已經遺失。

```javascript
function getCallStack() {
  var stack = [];
  for (var f = getCallStack.caller; f; f = f.caller) {
    stack.push(f);
  }
  return stack;
}

function f(n) {
  return n === 0 ? getCallStack() : f(n - 1);
}
var trace = f(1); // 無限迴圈
```

堆疊查閱功能是非標準的，移植性不高，應用層面也不廣。
在 ES5 strict mode 是被禁止的。
最好的策略是禁止使用堆疊查閱功能，如果目的是為了除錯，那就使用互動式的除錯器即可。

## 最後的實作

```javascript
function level1 () {
    console.log("level 1:", arguments.callee?.name, level1.caller?.name);
    level2();
}

function level2 () {
    console.log("level 2:", arguments.callee?.name, level2.caller?.name);
    level3();
}

function level3 () {
    console.log("level 3:", arguments.callee?.name, level3.caller?.name);
}

level1()
// level 1: level1 undefined
// level 2: level2 level1
// level 3: level3 level2
```