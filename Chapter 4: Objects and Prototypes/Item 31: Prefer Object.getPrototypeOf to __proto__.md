
# 條款 31 優先選用 Object.getPrototypeOf 而非 __proto__

>`__proto__` 它的存在和確切行為僅在 ECMAScript 2015 規範中被標準化為一項傳統功能。
優先選用符合標準的 Object.getPrototypeOf，而非 __proto__ 這個非標準特性。
>> `Object.getPrototypeOf()`方法返回指定物件的原型，等同公開 **([[Prototype]])**。

註： dunder 為 Double Underscore 的縮寫。

```javascript
let obj = {name: 'letter'};
obj.__proto__ === Object.getPrototypeOf(obj) // true
```
```javascript
Object.prototype.__proto__ = null; // 物件原型鏈 prototype.chain
```
---

要建立一個具有自訂原型連結的新物件，你可以使用 ES5 的 object.create。
```javascript
let empty = Object.create(null); // 沒有原型 (prototype) 的物件
'__proto__' in empty; // false
```

---

```javascript
function MyConstructor() {}

let obj = new MyConstructor()

Object.getPrototypeOf(obj) === obj.prototype // false
Object.getPrototypeOf(obj) === MyConstructor.prototype // true

MyConstructor.prototype // MyConstructor {}
obj.prototype // undefined

MyConstructor.prototype.constructor === MyConstructor  // true
Object.getPrototypeOf(MyConstructor) === Function.prototype // true
```
---

> `__proto__`屬性是一個簡單的訪問器屬性。

屬性訪問器提供了兩種方式用於訪問一個對象的屬性，它們分別是點號和方括號。

- 點號標示法
```javascript
get = object.property;
object.property = set;
```

- 方括號表示法
```javascript
get = object[property_name];
object[property_name] = set;
```

---

```javascript
"__proto__" in Object.create(null)
```

- false
  - Safari 版本14.0（15610.1.28.1.9, 15610）
  - FireFox 79.0（64 位元）
  - FireFox 83.0（64 位元）
  - Chrome 版本 87.0.4280.67 (正式版本) (x86_64)


---

[MDN Object.prototype.__proto__](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/proto)

[MDN in](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/in)

[MDN Object.getPrototypeOf()](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Object/getPrototypeOf)

[stack overflow Object.getPrototypeOf() vs .prototype
](https://stackoverflow.com/questions/38740610/object-getprototypeof-vs-prototype/38972040)

[ECMA Script 補充篇](https://read262.netlify.app/additional-ecmascript-features-for-web-browsers/additional-built-in-properties#sec-object.prototype.__proto__)

