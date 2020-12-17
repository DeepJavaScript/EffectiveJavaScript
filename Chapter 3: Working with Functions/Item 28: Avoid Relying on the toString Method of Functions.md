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

### ECMAScript 2019 定義了 Function.prototype.toString()

**19.2.3.5 Function.prototype.toString ( )**

當 `toString` method 被呼叫，會執行下面的步驟:

1. 把 func 放進 `this` 值
1. 若 func 是內建的 function 物件，就會回傳一個表示定義實作 func 的原始碼 String。這個表示形式必須有 NativeFunction 的語法。另外，若 func 有一個 `[[InitialName]]` internal slot 和 `func.[[InitialName]]` 是一個 String, 回傳的 String 必須是符合 func 的 NativeFunctionAccessor(opt) PropertyName 值的一部份
1. 如果 Type(func) 是一個 Object 並且 func 是一個 `[[SourceText]]` internal slot 和 `func.[[SourceText]]` 是一個。Unicode code points 的序列化，而且 `!HostHasSourceTextAvailable(func) ` 是 `true`，回傳 `!CodePointsToString(func.[[SourceText]])`
1. 如果 Type(func) 是一個 Object 並且 `IsCallable(func)` 是 `true`，就會回傳一個表示定義實作 func 的原始碼 String。這會表示成 NativeFunction 的語法。
1. Throw 一個 TypeError 例外。


NativeFunction:

- **function** NativeFunctionAccessor(opt) PropertyName`[~Yield, ~Await]`(opt) **(** FormalParameters`[~Yield, ~Await]` **) { [nativecode] }**

NativeFunctionAccessor:

- **get**
- **set**