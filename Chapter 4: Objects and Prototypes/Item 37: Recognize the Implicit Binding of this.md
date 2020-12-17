# 條款 37 認出 this 的隱含繫結

**戡誤**

    Item 37 (已戡誤)
    According to the page "This seemingly simple code has a major but subtle bug: The callback passed to lines.map refers to this, expecting to extract the regexp property of the CSVReader object. But map binds its callback's receiver to the lines array, which has no such property."
    However, if the second parameter of map is not in use, the global object (in this case window) associated with callback will be used instead, and not the lines array. (reported by Tan Gek Hua)


## 問題

```javscript
var reader = new CSVReader();
reader.read("a,b,c\nd,e,f\n"); // [["a,b,c"], ["d,e,f"]]
```


`CSVReader` 有一個 `regexp` 想要在 `read()` 時使用。

```javascript
function CSVReader (separators) {
  this.separators = separators || [","];
  this.regexp = new RegExp(this.separators.map(function (sep) {
    return "\\" + sep[0];
  }).join("|"));
}

CSVReader.prototype.read = function (str) {
  var lines = str.trim().split(/\n/);
  return lines.map(function (line) {
    return line.split(this.regexp); // wrong this!
  });
};
```

## function 當作 callback 時

### 1. 可以使用 map 的第二參數, 可以指定 callback 的 `this`

```javascript
//...
return lines.map(function (line) {
  return line.split(this.regexp);
}, this);  // 第二參數
//...
```

在此的 this 由 `reader.read()` 決定成 reader 的 instance。
若 read() 換成另種呼叫方式， this 則會改變，也許會造成此方法出錯。

### 2. 用變數暫存 `this`

```javascript
//...
const self = this;  // 暫存 this
return lines.map(function (line) {
  return line.split(self.regexp);
});
//...
```

### 3. 用 function method `.bind()`

```javascript
//...
return lines.map(function (line) {
  return line.split(this.regexp);
}.bind(this));  // 用 bind
//...
```

### 4. 用 arrow function (推薦)

```javascript
//...
return lines.map(line => {
  // arrow function
  return line.split(this.regexp);
});
//...
```

### 5. 也可以用閉包 closure 處理

```javascript
function CSVReader (separators) {
  this.separators = separators || [","];
  this.regexp = new RegExp(this.separators.map(function (sep) {
    return "\\" + sep[0];
  }).join("|"));
}

// 用 closure 閉包儲存 regexp
function split (regexp){
  return function(line) {
    return line.split(regexp);
  }
}
CSVReader.prototype.read = function (str) {
  var lines = str.trim().split(/\n/);
  return lines.map(split(this.regexp));
};
```

## this 是一種 runtime 的產物。

![](https://i.imgur.com/wHNKgfN.png)

書中提到的例子是 arrow function 出現之前 function 當 callback 發生的問題

