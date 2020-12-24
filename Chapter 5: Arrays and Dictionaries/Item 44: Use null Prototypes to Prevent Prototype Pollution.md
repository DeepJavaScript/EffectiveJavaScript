# 條款 44 使用 null 原型來防止原型汙染

**戡誤**(已戡誤)

    Item 44:
    Page 117, first code example, Object.getPrototypeOf(o) should be Object.getPrototypeOf(x)
    改符號 o -> x 用 x 與其它 code example 一致。

避免原型污染的問題最簡單的方法之一，就是讓它變得不可能。

ES5 之前，沒有標準的方法建立一個空原型 (empty prototype) 的新物件。

```javascript
function c () {}
c.prototype = null
var x = new c();
Object.getPrototypeOf(x) === null // false
Object.getPrototypeOf(x) === Object.prototype // true
```

但其實用這樣就可以了，同理 `Object.create(null)` 效果一樣

```javascript
function c () {}
c.prototype = null
var x = Object.setPrototypeOf({}, null)
Object.getPrototypeOf(x) === null // true
```

ES5 出現可以指定物件原型的標準語法 `Object.create()` 可以指定物件原型

不推薦的非標準做法 `__proto__`

因為 `__ptoro__` 是 getter/setter 並不是 property 但是重新 assign 之後就會變成 property


```javascript
var x = { __proto__: null }
x instanceof Object; // false
Object.getPrototypeOf(x) === null // true

var x = Object.setPrototypeOf({}, null)
x instanceof Object; // false
Object.getPrototypeOf(x) === null // true

function c () {};
c.prototype = null;
var x = new c();
x instanceof Object; // true: x 是一種 Object
Object.getPrototypeOf(x) === null // false: x 不是一種 null
```











