# 條款 17 優先選用間接的 eval 而非直接的 eval

- 多數函式可存取被定義之處的範疇。
  - 編譯時期 (compile time)。
- eval 函式可存取被呼叫之處的完整範疇。
  - 執行時期 (runtime) 才提供它們的範疇，導致 JS 引擎無法先進行優化機制。

### 作為一種妥協，標準化後呼叫 eval 區分兩種不同方式。
- 呼叫時涉及識別字 (identifier) eval，就被視為「直接」呼叫。
  - 編譯器需要確保所執行的程式能夠完全地存取呼叫者的區域範圍 (local scope)。
  - 呼叫效能成本非常昂貴，包含所有包圍它們的更外圍函式，一直到程式的最頂層，都將變得緩慢。

```javascript
var x = 'global';
function test(){
  var x = 'local';
  return eval('x'); // 直接的 eval(direct eval)
}

test() // 'local'
```

> warning
註： 圍在 (任意數目) 的括號。例：(eval)('x')、(((eval)))('x');

- 將函式繫結至一個不同的變數名稱，透過替代名稱來呼叫它，就被視為「間接」呼叫。
  -  會在全域範疇 (global scope) 中對它的引數求值 (evaluates)，使得其中程式碼無法存取任何區域範疇。

```javascript
var x = 'global';
function test(){
  var x = 'local';
  var f = eval;
  return f('x'); // 間接的 eval(indirect eval)
}

test() // 'global'
```

同等於如下：

```javascript
var x = 'global';
function test(){
  var x = 'local';
  return (0, eval)('x'); // 間接的 eval(indirect eval)
}

test() // 'global'
```

> warning
註： 運算式定序運算子 (expression sequencing operator，也就是`,`)，再配合一個顯然沒有意義的數字字面值。例：(0,eval)('x');

> 0 數字字面值會先被估算，但其值會被忽略，而括號起來的有序運算式 (parenthesized sequence expression) 會產生 eval 函式。**唯一值重要差異在於，整個呼叫運算式會被視為一次間接的 eval 呼叫。**

---

![eval(x)](https://i.imgur.com/trqPcma.png)

> The eval code cannot instantiate variable or function bindings in the variable environment of the calling context that invoked the eval if the calling context is evaluating **formal parameter** initializers or if either the code of the calling context or the eval code is **strict mode** code. Instead such bindings are instantiated in a new VariableEnvironment that is only accessible to the eval code. **Bindings introduced by let, const, or class declarations are always instantiated in a new LexicalEnvironment.**
let，const 或 class 聲明引入的綁定始終在新的 LexicalEnvironment 中實例化。

[ECMAScript 18.2.1 eval(x)](https://tc39.es/ecma262/#sec-eval-x)

### 嚴格模式

```javascript
var x = 'global';

function test(){
  'use strict'
  eval("var x = 'local'"); // 直接的 eval(direct eval)
  return x;
}

test() // 'global'
```
> 嚴格模式無法宣告變數，所以此函式是透過向外查找到 ｘ 變數，才能正常運行。

```javascript
var x = 'global';

function test(){
  'use strict'
  eval("var y = 'local'"); // 直接的 eval(direct eval)
  return y;
}

test() // Uncaught ReferenceError: y is not defined
```
> 嚴格模式無法宣告變數，所以無法正常運行。

```javascript
var x = 'global';

function test(){
  var x = 123;
  eval("var x = 'local'"); // 直接的 eval(direct eval)
  return x;
}

test() // 'local'
```
> 嚴格模式無法宣告變數，但因函式範疇早已宣告 var x，此時等於直接對 x 重新賦值，才能正常運行。

```javascript
var x = 'global';

function test(x = 'local'){
  'use strict'
  return eval('x'); // 直接的 eval(direct eval)
}

test() // Uncaught SyntaxError: Illegal 'use strict' directive in function with non-simple parameter list
```
> 嚴格模式下，使用形參 formal parameter，無法順利運行。

```javascript
var x = 'global';

function test(){
  const x = 123;
  // 無使用嚴格模式，所以允許宣告變數，並且查找不到 const x 變數。
  eval("const x = 'local'"); // 直接的 eval(direct eval)
  return x;
}

test() // 'local'，有無使用嚴格模式無差異。
```
> let，const 或 class 聲明引入的綁定始終在新的 LexicalEnvironment 中實例化。如一般情況下，重新賦值，會顯示 Uncaught TypeError: Assignment to constant variable.

**註： 使用 eval 函式時，如引數帶有 const、let 宣告變數，就要額外小心。**

---

- eval 函式 (直接) 使用 var 變數宣告時，作用在函式範疇。
- eval 函式 (間接) 使用 var 變數宣告時，作用在全域範疇。

```javascript
var x = 'global';
function test(x = 'local'){
  return (0,eval)("x = 123"); // 間接的 eval(indirect eval)
}

test() // 123
x // window.x
```

---

---

`eval()` 有一個秘密武器: 它不只是函式

多數函式可以存取它們被**定義**之處的 scope
eval 可以存取它被**呼叫**之處的 scope

在 complier 設計上，為了最佳化函式呼叫的速度，將一般函式和 eval 呼叫分開處理

**間接的用法** 

不會存取到任何的 local scope
改到 global

```javascript
var y = "global"; 
function test(src) {
    var f = eval
    f(src);
    return y; 
}
console.log(test("var y = 'local';")); // "local"
console.log(test("var z = 'local';")); // "local"
```

**奇特的呼叫方式**

改到 global

```javascript
var y = "global"; 
function test(src) {
    (0, eval)(src);
    return y; 
}
console.log(test("var y = 'local';")); // "local"
console.log(test("var z = 'local';")); // "local"
```

- 直接呼叫 evel 要連同包含它的外圍函式一起呼叫，甚至包含它們的所有外圍函式，都要考慮，所以才慢
- 間接呼叫 evel 就是在 global 呼叫
