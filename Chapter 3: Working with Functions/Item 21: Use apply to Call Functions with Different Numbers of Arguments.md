# 條款 21 使用 apply 以不同數目的引數來呼叫函式

method 的 this 是呼叫 method 的物件。
但是我們有時需要自定義某個函式(包含 function/method) 的 this，指定成我們想要的物件。
但是該物件的屬性並不擁有這個函式，無法直接使用。

沒錯！此條款的前提和上一個一模一樣。

但是，卻有著小小的不同之處。

`.apply(NULL, arguments)` apply 是用在將 Array-like 當參數使用的時候。

更正確的說法，在 function 中要把 arguments 丟給另一個 function 當參數，但是又無法確定自己的參數長度時，就可以使用 `.apply(NULL, arguments)`

```javascript 
function do () {
  globalFun.apply(NULL, arguments)
}

do (23,789,0,247,89)
do (33,78)
```

