# 條款 52 優先使用陣列字面值而非 Array 建構式


**戡誤**

    Item 52
    Things to Remember

    "The Array constructor behaves differently if its first argument is a number."

    should be

    "The Array constructor behaves differently if its only argument is a number."

    reported by Hemant Singal


```javascript
var a = new Array(); // 不可靠
var b = []; // 可靠
```

因為原型鏈會有被修改的風險，同理 constructor 也有被修改的風險，而字面值完全不會有被修改的風險，所以字面值可以保證你獲得你想要的型別，而 constructor 不保證。

而且 `new Array()` 他只接收一個參數為數字的時候，會直接建立一個 length 為該數字的 Array：

```javascript
new Array(3); // [ <3 empty slots> ] 而不是 [3]

new Array(1, 2); // [1, 2]
new Array('1'); // ['1']
```

厲害的是使用 `Array` 時就算不寫 `new` 也可以喲：）
