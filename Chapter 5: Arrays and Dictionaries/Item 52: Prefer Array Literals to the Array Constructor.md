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

因為原型鍊會有被修改的風險，同理 constructor 也有被修改的風險，而字面值完全不會有被修改的風險。

所以字面值可以保證你獲得你想要的型別，而 constructor 不保證。
