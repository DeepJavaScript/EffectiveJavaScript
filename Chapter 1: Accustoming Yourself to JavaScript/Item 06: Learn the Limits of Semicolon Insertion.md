# 條款 06 了解分號插入的限制

要擔心的太多了，乾脆都不要省略分號就好啦 !!!
but!!
JavaScript 會強制加入分號!!

- 會插入分號
  1. 分號只會被插入在一個 `}` 語彙單元(token)之前
  2. 一或多個 newlines (換行)之後
  3. 程式輸入的結尾
  4. 只有在下一個輸入語彙單元(input token)無法被解析(parse)時，才會加入分號
     - 也就是說，它是一種錯誤更正的機制
- 不會插入分號
  1. 常出問題的開頭字元: `(` `[` `+` `-` `/` 不會插入分號
     - 特例: 正規表示式 `/` 開頭
  2. 受限的產生規則(restricted productions)兩個語彙間，不允許 newline 的存在。
     - `return`, `throw`, 明確的`label`的`break`或`continue`, 後綴的`++`或`--`。
  3. 分號永遠不會被插入一個 `for ()` 小括號裡面作為分隔符號，或是插入作為一個空述句
  4. 迴圈裡不執行任何 code 時，不會在迴圈內自動加上分號
     - `while(true) //error`
     - `while(true); // ok

```javascript
a = b;
var a;
f();
```

```javascript
var a;
a = b(
  //不插入分號: b is not a function
  f()
);
```

受限的產生規則(restricted productions)
兩個語彙間，不允許 newline 的存在。
`return`, `throw`, 明確的`label`的`break`或`continue`, 後綴的`++`或`--`。

```javascript
return {};
```

```javascript
return;
{
}
```

### 條款 06 了解分號插入的限制

JavaScript 的分號錯誤修正機制：

#### 分號會插入在：

- `}` 之前
  ```javascript
  function foo() {
    return true;
  }
  function foo() {
    return true;
  }
  ```
- 一行的尾端

  ```javascript
  function foo(x) {
    return a + x;
  }

  function foo(x) {
    return a + x;
  }
  ```

- 一個程式的結尾
  :question: 這邊不懂
- `return`、`throw` 的後換行
  ```javascripte=
  function foo() {
      return
      {}
  }
  function foo() {
      return;
      {}
  }
  ```
- `break` 跟 `continue` 的後換行
  ```javascript
  for (let i = 0; i++; i < 3) {
    if (i === 2) {
      break;
    }
  }
  for (let i = 0; i++; i < 3) {
    if (i === 2) {
      break;
    }
  }
  ```
- `++` 跟 `--` 後綴運算子的前換行

  ```javascript
  a
  ++

  a;
  ++
  ```

#### 注意，分號不會插入在：

- `for 的條件式`
  ```javascript
  for(
      let i    // 不會插入分號
      i++      // 不會插入分號
      i<3      // 不會插入分號
  ){
      return i * 2;
  }
  ```
- `while` 的條件式
  ```javascript
  while(true)   // 不會加入分號
  ```

#### 注意 `(` `[` `+` `-` `/`

`(` `[` `+` `-` `/` 可能被解析為上一行的後續程式碼，所以要在以這些符號開頭的述句前加上分號：

```javascript
a + b(function () {})();

// 會被解析成
a + b(function () {})();
```

> [Automatic Semicolon Insertion | read262](https://read262.netlify.app/ecmascript-language-lexical-grammar/automatic-semicolon-insertion#sec-rules-of-automatic-semicolon-insertion)
> 不加分號要記太多規則了，所以一定要加分號！

## 規則

會自動插入分號 (所以可省略分號)：

- 一個 `}` token 之前 (即區塊的結尾)
- 一個或多個 newlines (換行) 之後 (即一行的結尾)
- 在程式輸入 (program input) 的結尾
- 在下一個 input token 無法被解析時，才會插入分號

不會自動插入分號：

- `(`、`[`、`+`、`-` 和 `/` 可能會被當作 expression operator 或陳述句的前綴，視 context 而定
  - 因此要注意以運算式為結尾的陳述句，例如：賦值陳述句
- `for` 迴圈的 header 內，以及 empty statement 一定要自己加分號
  - 否則會解析錯誤

## restricted productions

以下是 Spec 對 restricted productions (受限的產生規則) 的建議：

- 後綴 `++` 或 `--` 運算子 (例如：`a++` )：
  - 應與運算元在同一行
- `break` 或 `continue` 陳述句中的 label identifier：
  - 應與 `break` 或 `continue` token 在同一行
- `return` 或 `throw` 陳述句中的運算式：
  - 應與 `return` 和 `throw` token 在同一行
- `yield` 表達式中的賦值運算式：
  - 應與 `yield` token 在同一行

若沒有按照 restricted productions 的建議做 (即在這些 token 之前加上 newline)，就會在以下這些位置自動插入分號：

- 在 `++` 或 `--` token 之前
- 在 `continue`、`break`、`return`、`throw` 或 `yield` token 之後

> 可參閱 spec 對「[Automatic Semicolon Insertion](https://read262.netlify.app/ecmascript-language-lexical-grammar/automatic-semicolon-insertion)」的定義。

補充：
書中提到下面爛例不應使用 truthiness 測試來初始化 `x` 和 `y` 欄位 (違反條款 42 建議的)：

```javascript
function Point(x, y) {
  this.x = x || 0;
  this.y = y || 0;
}
```

其實可用 TC39 提案的 [Nullish Coalescing](https://github.com/tc39/proposal-nullish-coalescing) 來解決：

```javascript
function Point(x, y) {
  this.x = x ?? 0;
  this.y = y ?? 0;
}
```

規則：

1. 分號只會被插入在以下位置：
   - 一個 `}` token 之前
   - 一個或多個 newlines (換行) 之後
   - 在程式輸入 (program input) 的結尾

即只能在一行的結尾、區塊的結尾，或是程式的結尾省略分號，所以下面是合法的函數：

```javascript
function square(x) {
  var n = +x;
  return n * n;
}
function area(r) {
  r = +r;
  return Math.PI * r * r;
}
function add1(x) {
  return x + 1;
}
```

下面是不合法的：

```javascript
function area(r) { r = +r return Math.PI * r * r } // error
```

2. 只在下一個 input token 無法被解析時，才會插入分號
   - `(`、`[`、`+`、`-` 和 `/` 可能會被當作 expression operator 或陳述句的前綴、視 context 而定
     - 因此要注意以運算式為結尾的陳述句，例如：賦值陳述句

即分號插入是一種錯誤更正的機制。

例如：下面會被正確地解析成單一陳述句

```javascript
a = b(f());

// 解析後：
a = b(f());
```

```javascript
var b = (a) => a + 1;
var f = () => 20;

a = b(f());
console.log(a); // 21
```

例如：會被解析成兩個個別的陳述句，因為下面會被解析錯誤

```javascript
a = b
f();

// 解析後：
a = b f();
```

但我在 Chrome 執行不會解析錯誤，可以跑：

```javascript
var b = (a) => a + 1;
var f = () => {
  console.log("fn");
};

a = b;
f(); // fn
console.log(a); // a => a + 1
```

解析後很像這樣：

```javascript
a = b;
f();
```

例如：看起來很像兩個陳述句：賦值陳述句、呼叫陣列的 `map()` 的陳述句。但因為是以 `[` 為開頭，所以會被解析成一個陳述句：

```javascript
a = b[("r", "g", "b")].map((v) => v * 2);

// 解析後：
a = b[("r", "g", "b")].map((v) => v * 2);
```

而中括號內的 `"r", "g", "b"`，是以逗號分隔的運算式，JS 會從左邊開始往又求值 (evaluate)，並回傳最後一個子運算式 (subexpression) 的值，也就是 `"b"`，所以其實是存取 `b['b']`，並呼叫 `map()`：

```javascript
var b = { b: [1, 2] };

a = b[("r", "g", "b")].map((v) => v * 2);
console.log(a); // [2, 4]
```

若在 regex 的前面接著沒有以分號為結尾的賦值陳述句，原本 regex 的 `/` 就會變成除法運算子：

```javascript
a = b / Error / i.test(str) && fail();

// 解析後：
a = b / Error / i.test(str) && fail();
```

另一個範例：原本的 code 像這樣

```javascript
a = b; // 插入分號
var x; // 插入分號
f(); // 插入分號
```

若重構成這樣會出錯：

```javascript
var x; // 插入分號
a = b(
  // 不插入分號
  f()
); // 插入分號

// 解析後：
var x;
a = b(f());
```

另一個範例：IIFE：

```javascript
(function () {
  // ...
})()(function () {
  // ...
})()(
  // 解析後：
  function () {
    // ...
  }
)()(function () {
  // ...
})();

// 解法：

(function () {
  // ...
})();
(function () {
  // ...
})();
```

restricted productions (受限的產生規則) 的範例：會被解析成不帶 arg 的 `return`，後面接著 empty block，以及 empty statement：

```javascript
return;
{
}

// 解析後：
return;
{
}
```

3. 分號永遠不會被插入至一個 `for` 迴圈的 header 裡面作為分隔符號，或是插入作為一個 empty statement

`for` 迴圈的 header 中，要明確的加上分號，否則會解析錯誤：

```javascript
for (var i = 0, total = 1  // parse error
    i < n
    i++) {
  total += i
}

// 需改成：
for (var i = 0, total = 1;
    i < n;
    i++) {
  total += i;
}
```

empty statement (例如：empty body) 也要明確的加上分號：

```javascript
function infiniteLoop() { while (true) } // parse error

// 需改成：
function infiniteLoop() { while (true); }
```

自動化的分號插入 (automatic semicolon insertion)，程式解析技巧能夠在某些情境 (context) 推論出哪些分號可被省略。

分號規則如下：

1. 分號只會被插入在一個 } 語彙單元 (token) 之前 (行的結尾)，或是一或多個 newlines 之後 (區塊的結尾)，或是在程式輸入的結尾省略掉分號。

```javascript=
function area(r) { r = +r return Math.PI * r * r } // SyntaxError
```

2. 只有在下一個輸入語彙單元 (input token) 無法被解析時，才會插入分號，錯誤更正 (error correction) 的機制。

```javascript=
a = b
(f());
=> a = b(f()); 解析結果
```

需特別留意，五個常出問題的字元：圓括號 (、方括號 [、加號 +、減號 - 與 斜線號 /。其中每一個都可當作是一個運算式運算子 (expression operator)，或是一個述句的前綴 (prefix)，端視情境 (context) 而定。

```javascript=
a = b; // 插入分號
var x; // 分號在下一行
f(); // 此行為防衛性插入分號，避免上面兩行更動順序，造成結果與預期不相同。
```

- 受限的產生規則 (restricted productions)，兩個語彙單元之間不允許 newline 換行存在，例如最有害的案例 return 述句：在 return 關鍵字和它非必須的引數之間，必不能含有 newline。

```javascript=
return {}; // 預期結果

retrun
{}; // 非預期結果，解析三個個別的述句。
=> return; // 不帶引數的 return
{ } // 空區塊 (empty block)
; // 空述句 (empty statement)
```

#### 其他受限的產生規則 (restricted productions):

- 一個 throw 述句。
- 具有明確標籤 (label) 的 break 或 continue 述句。
- 一個後綴 (postfix) 的 ++ 或 -- 運算子。 消除程式碼的歧義：

```javascript=
a;
++b;
```

既然 ++ 可當作前後綴運算子，而後綴運算子前面不可以有一個 newline，就會被解析為：

```javascript=
a;
++b;
```

3. 分號永遠都不會被插入到一個 for 迴圈的標頭 (head) 裡面作為分隔符號，或是插入作為一個空述句。

```javascript=
for (var i = 0, total = 1 // 解析錯誤
  i < n
  i++) {
  total *= i
}
```

```javascript=
function infiniteLoop() { while (true) } // 解析錯誤。
function infiniteLoop() { while (true); } // 明確分號。
```
