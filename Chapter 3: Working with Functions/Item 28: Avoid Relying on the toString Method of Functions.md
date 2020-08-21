# 條款 28 避免仰賴函式的 toString 方法

在 JavaScript 中，可以透過 `toString()` 的方式將 function 轉成字串，但這其實會有幾個問題：

1. ECMAScript 標準並沒有針對 `toString()` 有任何的要求，這意謂著各家實作 JavaScript 引擎的供應商會實作出不同結果，因此同樣的程式碼在不同家的引擎上可能執行會失敗。
2. 由於 `bind` 是使用另一種語言實現(如 C++)，所以會是如下的呈現方式(表示不一定能夠透過 `toString()` 取得正確的 source code):

```javascript=
// function () { [native code] }
(function(x){
  return x + 1
}).bind(16).toString()
```
