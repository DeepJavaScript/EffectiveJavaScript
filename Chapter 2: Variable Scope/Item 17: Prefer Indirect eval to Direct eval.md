# 條款 17 優先選用間接的 eval 而非直接的 eval

`eval()` 有一個秘密武器: 它不只是函式

多數函式可以存取它們被**定義**之處的 scope
eval 可以存取它被**呼叫**之處的 scope

在 complier 設計上，為了最佳化函式呼叫的速度，將一般函式和 eval 呼叫分開處理

**間接的用法** 

不會存取到任何的 local scope
改到 global

```javascript
var y = "global"; 
function test(src) {
    var f = eval
    f(src);
    return y; 
}
console.log(test("var y = 'local';")); // "local"
console.log(test("var z = 'local';")); // "local"
```

**奇特的呼叫方式**

改到 global

```javascript
var y = "global"; 
function test(src) {
    (0, eval)(src);
    return y; 
}
console.log(test("var y = 'local';")); // "local"
console.log(test("var z = 'local';")); // "local"
```

- 直接呼叫 evel 要連同包含它的外圍函式一起呼叫，甚至包含它們的所有外圍函式，都要考慮，所以才慢
- 間接呼叫 evel 就是在 global 呼叫
