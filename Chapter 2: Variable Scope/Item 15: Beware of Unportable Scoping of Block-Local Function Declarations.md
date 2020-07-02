# 條款 15 小心 Block-Local 函式宣告造成不可移植的範疇

內容完全過時了。

1. function name 也擁有 hoisting 
2. "use strict"; 以 block-scope 為主

```javascript
function f() { return "global"; } 

function test(x) {
    function f() { return "local"; }
    
    var result = [];
    if (x) {
        result.push(f());
    }
    result.push(f());
    return result; 
}
```

test(true); // ["local", "local"]
test(false); // ["local"]


```javascript
function f() { return "global"; } 

function test(x) {
    "use strict";  //沒有這個，會報錯 f is not a function
    var result = [];
    if (x) {
        function f() { return "local"; }
        result.push(f());
    }
    result.push(f());
    return result; 
}
```

test(true); // ["local", "global"]
test(false); // ["local"]

