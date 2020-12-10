# 條款 34 將方法儲存在原型上

在 instance 上定義方法成員和資料成員，所以各 instance 都有各自的方法 (雖然行為一樣)：

```javascript
function User(name, passwordHash) {
  this.name = name;
  this.passwordHash = passwordHash;
  this.toString = function() {
    return "[User " + this.name + "]";
  };
  this.checkPassword = function(password) {
    return hash(password) === this.passwordHash;
  };
}

var user1 = new User('A', '0ef33a');
var user2 = new User('B', 'e79106');
var user3 = new User('C', '8ec64b');
```

![](https://i.imgur.com/fDVC1TH.png)

在 prototype 上定義方法，藉由 prototype 來共用方法，所有 instance 都可以存取到，不用在每個 instance 上重複複製多份的方法和 property：

```javascript
function User(name, passwordHash) {
  this.name = name;
  this.passwordHash = passwordHash;
}
User.prototype.toString = function() {
  return "[User " + this.name + "]";
};
User.prototype.checkPassword = function(password) {
  return hash(password) === this.passwordHash;
};

var user1 = new User('A', '502d6c');
var user3 = new User('C', '8ec64b');
var user2 = new User('B', 'b01913');
```

![](https://i.imgur.com/Vym06KW.png)

你可能會想放在 instance 上可以最佳化方法查找 (lookup) 的速度，例如：`user1.toString()` 不需要搜尋原型鏈來找到 `toString`。但現代 JS 引擎已優化原型查找的動作，所以將方法複製到 instance 上不會提升效能，反而會佔用更多記憶體。

因此，簡易將重複的方法放在 prototype 上，讓 instance 共用。

## 結論

用 Class 就對了。