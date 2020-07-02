# 條款 06 了解分號插入的限制

要擔心的太多了，乾脆都不要省略分號就好啦 !!!
but!!
JavaScript會強制加入分號!!

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
    4. 迴圈裡不執行任何code時，不會在迴圈內自動加上分號
        - `while(true)  //error`
        - `while(true); // ok

```javascript
a = b
var a
(f())
```

```javascript
var a
a = b  //不插入分號: b is not a function 
(f())
```


受限的產生規則(restricted productions)
兩個語彙間，不允許 newline 的存在。
`return`, `throw`, 明確的`label`的`break`或`continue`, 後綴的`++`或`--`。

```javascript
return {};
```

```javascript
return
{}
;
```

### 條款 06 了解分號插入的限制
JavaScript 的分號錯誤修正機制：
#### 分號會插入在：
- `}` 之前
    ```javascript
    function foo() {return true}
    function foo() {return true;}
    ```
- 一行的尾端
    ```javascript
    function foo(x) { 
        return a + x
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
    for(let i=0 ; i++ ; i<3) {
        if(i === 2) {
            break
        }
    }
    for(let i=0 ; i++ ; i<3) {
        if(i === 2) {
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
a + b
(function(){})()

// 會被解析成
a + b(function(){})()
```
> [Automatic Semicolon Insertion | read262](https://read262.netlify.app/ecmascript-language-lexical-grammar/automatic-semicolon-insertion#sec-rules-of-automatic-semicolon-insertion)
