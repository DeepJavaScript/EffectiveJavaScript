# 條款 16 避免使用 eval 來建立區域變數

```javascript
var y = "global"; 
function test(src) {
    eval(src);
    return y; 
}
console.log(test("var y = 'local';")); // "local"
console.log(test("var z = 'local';")); // "global"
```

誤用: 允許 eval 介入範疇

要用，要給 scope

```javascript
var y = "global"; 
function test(src) {
    (() => eval(src))();  // eval 執行在自己的 local scope
    return y; 
}
console.log(test("var y = 'local';")); // "global"
console.log(test("var z = 'local';")); // "global"
```