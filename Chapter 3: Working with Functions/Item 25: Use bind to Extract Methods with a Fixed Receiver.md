# 25. 使用 `bind` 擷取出具有固定接收者的方法

書中說的「固定接收者 (fixed receiver)」即為 `this` binding。

有一種作法是將物件內的方法，作為 callback 傳入高階函數來呼叫。在呼叫該 callback 時，  `this` 可能不是該方法的 context object。

例如：想使用 `Array#forEach()`，並透過呼叫 `buffer.add` 將字串陣列中的每個字串複製到 `buffer.entries` 內：

```javascript
var buffer = {
  entries: [],
  add(s) {
    console.log('this:', this);
    this.entries.push(s);
  },
  concat() {
    return this.entries.join("");
  }
};
```

但會出錯，因為在透過 `Array#forEach()` 呼叫 callback 時，`buffer.add` 內的 `this` 是全域物件 (在瀏覽器為 `window` )，不是原本預期的 `buffer` 物件：

```javascript
var source = ["867", "-", "5309"];
source.forEach(buffer.add);  // error: entries is undefined
// this: Window {...}
// TypeError: Cannot read property 'push' of undefined
```

用 `Array#forEach()` 的第二個參數 `thisArg` 就可以設定 `this` binding：

```javascript
var source = ["867", "-", "5309"];
source.forEach(buffer.add, buffer);
buffer.join();  // "867-5309"
```

如果高階函數不能自訂 callback 內的 `this`，可建立 wrapper 函數，在裡面明確的呼叫函數 (此範例是用 context object 的方式來呼叫，即 `obj.func()`，所以 `this` 為 `obj` )：

```javascript
var source = ["867", "-", "5309"];
source.forEach(function(s) {
  buffer.add(s);
});
buffer.join(); // "867-5309"
```

在 ES5 提供了 `Function#bind()`，可設定 `this` binding，並回傳新的函數：

```javascript
var source = ["867", "-", "5309"];
source.forEach(buffer.add.bind(buffer));
buffer.join(); // "867-5309"
```