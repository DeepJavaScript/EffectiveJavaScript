# 條款 12 了解變數的拉升 (Variable Hoisting)

在此，我們先了解早期的語言的慣例與困難，再來了解 JavaScript 面對這樣的問題做了什麼設計，才可以了解 hoisting 為什麼是一個好的設計。

## 宣告 vs 定義

觀念上要了解「宣告」和「定義」是兩回事。

用 C 語言做範例

**宣告**

```c
int foo; // declare a variable
int bar(int, int); // declare a function
```

**定義**

```c
foo = 3; // define a variable

 // define a function
int bar(int bar1, int bar2) {
  // do something...
}
bar();
```

**限制**

使用(定義)變數或函數前，要先宣告。

```c
// define a function
int bar(int bar1, int bar2) {
  // do something...
}

bar();  // --> ok
```

函數呼叫，常常會在定義前想要執行。
但是若寫得像下面這樣，容易出錯。

```c
bar();  // --> error

// define a function
int bar(int bar1, int bar2) {
  // do something...
}
```

必須要先宣告函數，才能執行，而定義是不是寫在執行之前，已經不重要了。
所以才能夠讓函數定義，寫在任意的位置。

```c
int bar(int, int); // declare a function

bar();  // --> ok

// define a function
int bar(int bar1, int bar2) {
  // do something...
}
```

JavaScript 為了能夠讓函數能夠在任意的地方宣告 (其實是定義) 所以做了 hoisting 讓函數真正的宣告可以由 JavaScript 的引擎處理，開發者寫的宣告，其實是函數定義，就可以放在任意的位置了。

## hoisting 就是幫你「宣告」

由上面的描述，同理用在 `var` 宣告的變數，就可以得說通。
而函數表達式的 `var fun = funciton () {}` 這種寫法，也就可以理解了。

```javascript
function fun () { console.log('decare fun') }
var fun = function () { console.log('exp fun') }
fun(); // exp fun
```

```javascript
var fun = function () { console.log('exp fun') }
function fun () { console.log('decare fun') }
fun(); // exp fun
```

函數會先宣告，之後再宣告變數，經由定義的賦值行為將函數宣告式蓋掉。

## `var` 的 hoisting

ES6 之前，JS 只有函式範疇與全域範疇， ES6之後則有區塊範疇，一個經典例子為 for loop 中宣告的 `var i = 0;` ，不斷修改相同的 i ，所以值並不會有變。

```javascript
function isWinner(player, others) {
  var highest = 0;
  for (var i = 0, n = others.length; i < n; i++) {
    var player = others[i];
    if (player.score > highest) {
      highest = player.score;
    }
  }
  return player.score > highest;
}

// equal to 
function isWinner(player, others) {
  // hoisting
  var i;
  var highest;
  var player;

  // origin code
  highest = 0;
  for (i = 0, n = others.length; i < n; i++) {
    player = others[i];
    if (player.score > highest) {
      highest = player.score;
    }
  }
  return player.score > highest;
}
```

## `try...catch` 中的 `catch`，則為區塊範疇

> JavaScript 支援 `lexical scop`，不支援 `block scop`。
例外: `throw` 到 `catch` 的變數，像是 function 的變數

```javascript
function test() {
  var x = "var", result = [];
  result.push(x);
  try {
    throw "exception";
  } catch (x) {
    result.push(x);
    return result;
  }
}
test(); // ["var", "var"]
```

- [ES6 要了解 TDZ](https://blog.techbridge.cc/2018/11/10/javascript-hoisting/)
- [13.3.1 Let and Const Declarations](https://www.ecma-international.org/ecma-262/6.0/#sec-let-and-const-declarations)
- [13.3.2 Variable Statement](https://www.ecma-international.org/ecma-262/6.0/#sec-variable-statement)