# 條款 13 使用即刻調用的函式運算式來建立區域範疇

IIFE 唸 "iffy"

```javascript
function wrapElements(a){
  var result = [], i , n;
  for(i = 0, n = a.length; i < n; i++){
    result[i] = function(){ return a[i];};
    console.log('inside', i);
  }
  console.log('outside', i);
  return result;
}

var wrapped = wrapElements([10, 20 ,30 ,40 ,50]);
var f = wrapped[0]; 
f() // undefined
```

- 程式執行時期 (runtime) 進入到一個範疇 (scope) 時，會為該範疇內的每一個變數繫結 (variable binding) 都在記憶體中配置 (如果區塊範疇沒有宣告，就參考外層區塊的變數記憶體位置) 一個「位置」(slot)。 (所以跨範疇跟不跨範疇都會產生 bind 的行為。)
- wrapElements 函式繫結三個區域變數：result、i 以及 n。在這個迴圈的每次迭代動作中，迴圈主體會為嵌套函式配置一個 closure。
- 巢狀函式被建立時，儲存的是對 i 的一個參考 (reference)，而不是當下值。既然 i 的值會在每個函式被建立之後改變，那些內層函式所看見的會是 i 的最終值。這就是閉包 (closures) 的關鍵所在：

> 註：範例因當下返回一組陣列，陣列元素皆是函式，閉包存取變數皆為同一個 slot (共用)，導致遞增直到 i 超出陣列索引範圍為止，實際呼叫時查找當下變數 i 索引值為 5，即回傳 undefined。([10, 20 ,30 ,40 ,50] 陣列長度為 4)

```javascript
function wrapElements(a){
  var result = [];
  for(var i = 0, n = a.length; i < n; i++){
    (function() {
      var j = i;
      result[i] = function(){ return a[j];};
      console.log('inside', i, j);
    })();
  }
  console.log('outside', i, j);
  return result;
}

var wrapped = wrapElements([10, 20 ,30 ,40 ,50]);
var f = wrapped[0]; 
f() // 10
```
或是把 ｊ的值透過引數傳入：
```javascript
function wrapElements(a){
  var result = [];
  for(var i = 0, n = a.length; i < n; i++){
    (function(j) {
      result[i] = function(){ return a[j];};
      console.log('inside', i, j);
    })(i);
  }
  console.log('outside', i, j);
  return result;
}

var wrapped = wrapElements([10, 20 ,30 ,40 ,50]);
var f = wrapped[0]; 
f() // 10
```
用立即函式來建立一個區域範疇，並且立即呼叫的話，可以讓內部的變數是當下的值，而不是參考外部的值。
- 內部的 j 的區域只限於立即執行函式這個區域範疇內，會被賦予當下的 i 值。
- 外部的 i 區域依然是提升宣告，所以值會參考同一個記憶體內的值，還是會接到 for 迴圈這個範疇以外的 i 值。
- 所以呼叫第 0 個函式，函式內的 j 會是存取當下的 0。
- `f()` 的回傳值會是 a 陣列第 0 個 10。

## 重點整理
- 理解繫結 (binding) 與指定 (assignment) 的之間的差異。
- Closures 所儲存的是對它們外層變數的參考 (reference)，而非那些變數的值 (value)。
- 用即刻調用的函式（IIFEs）可以建議區域範疇。
- 小心區塊（block）被包在一個 IIFE 之後可能產生的行為變化。
  - 任何導致會跳到區塊外的 break 跟 continue 都是不合法的。(基本上這兩個不會寫在函式內，也會是不合法的狀態)。
  - IIFE 會改變區塊所參考到的 this 跟 argument。
