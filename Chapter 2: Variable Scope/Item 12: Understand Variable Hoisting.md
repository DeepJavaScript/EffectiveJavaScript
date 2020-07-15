# 條款 12 了解變數的拉升(Variable Hoisting)

JavaScript 支援 `lexical scop`，不支援 `block scop`。
例外: `throw` 到 `catch` 的變數，像是 function 的變數



ES6 之前，JS 只有函式範疇與全域範疇， ES6之後則有區塊範疇，一個經典例子為 for loop 中宣告的 `var i = 0;` ，不斷修改相同的 i ，所以值並不會有變。

```javascript
function isWinner(player, others) {
  var highest = 0;
  for (var i = 0; n = others.length; i < n; i++) {
    var player = others[i];
    if (player.score > highest) {
      highest = player.score;
    }
  }
  return player.score > highest;
}

// equal to 
function isWinner(player, others) {
  var highest = 0;
  var i;
  for (i = 0; n = others.length; i < n; i++) {
    var player = others[i];
    if (player.score > highest) {
      highest = player.score;
    }
  }
  return player.score > highest;
}
```

而 `try...catch` 中的 `catch`，則為區塊範疇

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