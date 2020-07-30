# 條款 13 使用即刻調用的函式運算式來建立區域範疇

IIFE 唸 "iffy"

```javascript
function wrapElements(a){
  var result = [], i , n;
  for(i = 0, n = a.length; i < n; i++){
    result[i] = function(){ return a[i];};
  }
  return result;
}

var wrapped = wrapElements([10, 20 ,30 ,40 ,50]);
var f = wrapped[0]; 
f() // undefined
```

理解繫結 (binding) 與指定 (assignment) 的之間的差異。

- 程式執行時期 (runtime) 進入到一個範疇 (scope) 時，會為該範疇內的每一個變數繫結 (variable binding) 都在記憶體中配置一個「位置」(slot)。
- wrapElements 函式繫結三個區域變數：result、i 以及 n。在這個迴圈的每次迭代動作中，迴圈主體會為嵌套函式配置一個 closure。
- 巢狀函式被建立時，儲存的是對 i 的一個參考 (reference)，而不是當下值。既然 i 的值會在每個函式被建立之後改變，那些內層函式所看見的會是 i 的最終值。這就是閉包 (closures) 的關鍵所在：

> Closures 所儲存的是對它們外層變數的參考 (reference)，而非那些變數的值 (value)。

> 註：範例因當下返回一組陣列，陣列元素皆是函式，閉包存取變數皆為同一個 slot (共用)，導致遞增直到 i 超出陣列索引範圍為止，實際呼叫時查找當下變數 i 索引值為 5，即回傳 undefined。([10, 20 ,30 ,40 ,50] 陣列長度為 4)