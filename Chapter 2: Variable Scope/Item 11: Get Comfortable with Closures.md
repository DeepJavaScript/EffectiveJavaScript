# 條款 11 善於使用 Closures

了解 `closures` 只需要了解三件事
1. JavaScript 允許你存取定義在目前函數外變數的參考
2. 即使外層函數回傳後，內層函數也能存取到外層函數裡的變數(函數是物件)
3. 能夠更新外層變數(outer value)值

JavaScript 函式值 (function valus) 所包含的資訊，
不只是在函數中被呼叫、執行的程式碼。
儲存定義、參考到，並且包在範疇中的變數

函式能存取來自包含函式本身的範疇中所定義的變數，這個函式就是閉包 (closure)。

## JavaScript 允許你存取定義在目前函數外變數的參考

這裡在講的是一般的函數呼叫，函數可以使用全域變數

```javascript
function makeSandwich() {
  var magicIngredient = "peanut butter"; 
  function make(filling) {
    return magicIngredient + " and " + filling;
  }
  return make("jelly");
}
makeSandwich();
```

## 即使在外層函式執行結束後，從外層函數回傳出來的內層函式

即使在外層函式執行結束後，從外層函數回傳出來的內層函式，
還是能夠存取定義在外層函式外的變數參考，這就是閉包特性

由於函式就是物件，所以可以把函式當作「函式回傳值」回傳。

```javascript
function makeSandwich() {
  var magicIngredient = "peanut butter"; 
  function make(filling) {
    return magicIngredient + " and " + filling;
  }
  return make;
}
var f = makeSandwich();
f("jelly");
f("bananas");
f("marshmallows");
```

## 能夠更新外層變數(outer value)值

```javascript
function sandwichMaker(magicIngredient) {
  function make(filling) {
    return magicIngredient + " and " + filling; 
  }
  return make; 
}
var hamAnd = sandwichMaker("ham"); 
hamAnd("cheese"); // "ham and cheese" 
hamAnd("mustard"); // "ham and mustard"
var turkeyAnd = sandwichMaker("turkey"); 
turkeyAnd("Swiss"); // "turkey and Swiss" 
turkeyAnd("Provolone"); // "turkey and Provolone"
```
