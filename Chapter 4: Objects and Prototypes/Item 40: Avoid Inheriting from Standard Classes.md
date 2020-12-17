# 條款 40 避免繼承標準類別

標準程式庫 (standard library) 很小，包含 Array, Function, Date。

可能想要繼承它們產生子類別。

```javascript
function Dir(path, entries) {
  this.path = path;
  for (var i = 0, n = entries.length; i < n; i++) {
    this[i] = entries[i];
  }
}

Dir.prototype = Object.create(Array.prototype); // extends Array

var dir = new Dir("/tmp/mysite",
                ["index.html", "script.js", "style.css"]);

dir.length; // 0
```

用新的寫法，倒是可以

```javascript
class List extends Array {
  constructor() {
    super();
  }
}
```

這樣繼承 Array 的話 length 不會出錯。