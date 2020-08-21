# 條款 14 小心具名的函式運算式造成不可移植的範疇

> 不用考慮這件事了。
> 不過要知道 Function Declaration 的名稱用來做什麼的
> debugger 和 遞迴用的

函式有兩種

1. Function Statement/Function Declaration(函式陳述句、宣告式)  `function a () {}` 
2. Function Expression(函式表達式、表示式) `var a = function () {}`

> Function Statements (函式運算式、函式陳述句)
> 
> function Declaration
> named function expression
> 
> ```javascript
> function double(x) { return x * 2; }
> ```
> [name=書中這樣定義，在此還是用原本的]


名稱的用途

||變數名稱|函式名稱|
|-|-|-|
|Function Statement|-|用來呼叫 & 遞迴|
|Function Expression|用來呼叫~[下面的例子]~|用來遞迴|

```javascript
var f = function double(x) { return x * 2; }
f();      
// ƒ double(x) { return x * 2; }

double();
//Uncaught ReferenceError: double is not defined
//    at `<anonymous>`:3:1
//(anonymous) @ VM1460:3
```


> Function Expression 的名稱真正用途，在除錯。
一個 Function Statement 的名稱通常會用在它在的 stack 名稱。
[name=p.52]

#### named Function Expression 在 ES3 中的 bug
```javascript
var constructor = function () { return null; }
var f = function f () {
    return constructor();
}
f(); //{}，在 ES3 的環境中
```

1. scope 表示成一個物件 (ES5 已修正)

過去，執行 `f()` 後，並不會得到 `null` 而是產生一個新物件。
> 執行 `f()` 時
若是執行 `Object scope` 則是取得新物件。
若是執行 `function scope` 則是取得函數結果 `null` 。

2. 過去，會把具名函式當作宣告來 `hoist`  (現在不會了)

```javascript
var f = function g() { return 17; };
g(); //17，在不合標準的環境中

//在標準環境中要報錯
//VM3048:1 Uncaught ReferenceError: g is not defined
//    at `<anonymous>`:1:1
//(anonymous) @ VM3048:1

```
