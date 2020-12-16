# 條款 06 了解分號插入的限制

**戡誤**

    Item 6
    The Point example shouldn't use a truthiness test to initialize its x and y fields, as Item 54 recommends.


要擔心的太多了，乾脆都不要省略分號就好啦 ！！！
but ！！
JavaScript 會強制加入分號！！

> ### 會插入分號：

  - 分號只會被插入在一個 `}` 語彙單元 (token) 之前。
  - 一或多個 newlines (換行)之後。
  - 程式輸入的結尾。
  - 只有在下一個輸入語彙單元 (input token) 無法被解析 (parse) 時，才會加入分號，
     也就是說，它是一種錯誤更正的機制。

`}` 之前
```javascript
function foo() {
  return true
}
function foo() {
  return true;
}
```

一或多個 newlines (換行)之後。
```javascript
var a
    b =
    3;

var a;
    b =
    3;
```

一個程式的結尾
```javascript
function foo(x) {
  return a + x
}

function foo(x) {
  return a + x;
}
```

---

> ### 不會自動插入分號：

- 需特別留意，五個常出問題的字元：圓括號 (、方括號 [、加號 +、減號 - 與 斜線號 /。其中每一個都可當作是一個運算式運算子 (expression operator)，或是一個述句的前綴 (prefix)，端視情境 (context) 而定。
  - 因此要注意以運算式為結尾的陳述句，例如：賦值陳述句。
- `for` 迴圈的 header 內，以及 empty statement 一定要自己加分號，否則會解析錯誤。

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
- 在 `continue`、`break`、`return`、`throw` 或 `yield` token 之後。

若在 regex 的前面接著沒有以分號為結尾的賦值陳述句，原本 regex 的 `/` 就會變成除法運算子：

```javascript
a = b
/ Error / i.test(str) && fail();

// 解析後：
a = b / Error / i.test(str) && fail();
```

`for` 的條件式
```javascript
for(
    let i    // 不會插入分號
    i++      // 不會插入分號
    i<3      // 不會插入分號
){
    return i * 2;
}
```

`while` 的條件式
```javascript
while(true)   // 不會加入分號
```

`return`、`throw` 的後換行
```javascript
function foo() {
    return
    {}
}
function foo() {
    return;  // 受限的產生規則，插入分號。
    {}
}
```

`break` 跟 `continue` 的後換行
```javascript
for (let i = 0; i++; i < 3) {
  if (i === 2) {
    break;
  }
}
for (let i = 0; i++; i < 3) {
  if (i === 2) {
    continue;
  }
}
```

`++` 跟 `--` 後綴運算子的前換行
```javascript
a
++
b

a;
++b
```

---

### 資料來源

- [ECMAScript® 2021 Language Specification](https://read262.netlify.app/ecmascript-language-lexical-grammar/automatic-semicolon-insertion)·｀
- [11.9 Automatic Semicolon Insertion](https://tc39.es/ecma262/#sec-automatic-semicolon-insertion)
- [Understanding Automatic Semicolon Insertion in JavaScript](http://www.bradoncode.com/blog/2015/08/26/javascript-semi-colon-insertion/)
- [JavaScript Visualized: the JavaScript Engine](https://dev.to/lydiahallie/javascript-visualized-the-javascript-engine-4cdf)
- [Parser produces the (beautiful) syntax tree](https://esprima.org/demo/parse.html#)
- [【你應該了解的】抽象語法樹AST](https://juejin.im/post/5e942d926fb9a03c7d3d07a4)
