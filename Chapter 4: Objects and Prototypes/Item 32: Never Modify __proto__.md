# 條款 32 永不修改 `__proto__`

## 為了「可移植性」，要避免修改 `__proto__` 。

不是所有的平台都支援修改原型，移過去執行就會壞掉

### 正確的修改 `__proto__`

在 `__proto__` 尚未成為規範的一部份時，正確的取得 `__proto__` 必須要使用 `Object.getPrototypeOf()`。也就是說想要正確的修改 `__proto__` 必須使用 `Object.setPrototypeOf()`。

#### 直接賦值，永遠不會正確。

因為它是 PropertyDescriptor 的 getter/setter，不是單純的 property。

```javascript
function User(name) {
  this.name
}

function UserA(name) {
  this.name
}
const u = new User('Clark');
u.__proto__ = UserA.prototype;
u instanceOf UserA; // false
```

`__proto__` 變成 `u` 的屬性，但原本是 setter

#### 透過 `Object.setPrototypeOf()` 正確修改

要修改，要透過 `Object.setPrototypeOf(target, prototype)`

```javascript
function User(name) {
  this.name
}

function UserA(name) {
  this.name
}
const u = new User('Clark');
Object.setPrototypeOf(u, UserA.prototype);
u instanceOf UserA; // true
```



## 不要改的為了效能

JS 引擎最佳化，會因為修改 `__proto__` 失效，因為改變繼承結構。

## 不要改的最大原因，為了可預測性

保持每一個物件的繼承情況都是不變的，可以讓程式碼可預測。

如果想要自訂原型連結的新物件，可以使用 `Object.create()` 獲得。

如果沒有 ES5 的環境，可以參考 條款 33 (如果你的建構式不用 new 呼叫的話)

**語法實驗**

`Object.create(prototype)` 等同於使用 `Object.setPrototypeOf({}, prototype)`

```javascript
Object.create = function (prototype) {
    return Object.setPrototypeOf({}, prototype)
}
```

> 根據實驗結果，正確的自訂原型練的新物件，等同於要對新物件修改 `__proto__`
