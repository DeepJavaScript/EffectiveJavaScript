# 條款 55 為關鍵字引數接受選項物件

**戡誤**

    Item 55
    First extend() call needs opts passed in as its first argument. This applies to the code examples on both page 151 and 152.

如果 Constructor 必要的參數很多，那就會寫成這樣子：

```javascript
var alert = new Alert(
    100, 75, 300, 200, "Error", message,
    "blue", "white", "black", "error", true
);
```

如果參數一多，就沒辦法知道各個參數代表什麼，不過可以用接收一個 Object 來表示每個參數的意義（因為在 JavaScript 的 Object 都會有帶屬性名稱），這會讓每個參數都自有說明化：

```javascript
var alert = new Alert({
    x: 100,
    y: 75,
    width: 300,
    height: 200,
    title: "Error",
    message: message,
    titleColor: "blue",
    bgColor: "white",
    textColor: "black",
    icon: "error",
    modal: true
});
```

傳入 Object 的另外一個特色是所有的引數都是可選擇性的，例如當不需要 `x` 及 `y` 的時候就可以不傳：

```javascript
var alert = new Alert({
    width: 300,
    height: 200,
    title: "Error",
    message: message,
    titleColor: "blue",
    bgColor: "white",
    textColor: "black",
    icon: "error",
    modal: true
});
```

只是如果不傳的話就要在 constructor 上做可選引數的預設值判斷：

```javascript
function Alert(parent, message, opts) {
    opts = opts || {}; // default to an empty options object this.width = opts.width === undefined ? 320 : opts.width; this.height = opts.height === undefined
    ?
    240
        : opts.height;
    this.x = opts.x === undefined ?
        (parent.width / 2) - (this.width / 2) : opts.x;
    Item 55: Accept Options Objects
    for Keyword Arguments 151
    this.y = opts.y === undefined ?
        (parent.height / 2) - (this.height / 2) : opts.y;
    this.title = opts.title || "Alert";
    this.titleColor = opts.titleColor || "gray";
    this.bgColor = opts.bgColor || "white";
    this.textColor = opts.textColor || "black";
    this.icon = opts.icon || "info";
    this.modal = !!opts.modal;
    this.message = message;
}
```

另外如果有一兩個必要的引數就要把它們和 Object 分開：

```javascript
var alert = new Alert(app, message, {
    width: 150,
    height: 100,
    title: "Error",
    titleColor: "blue",
    bgColor: "white",
    textColor: "black",
    icon: "error",
    modal: true
});
```

也可以使用 `extend` 來讓處理預設值，如果引數物件中沒有某些可選屬性，就會把他補上去，例如：

```javascript
opts = extend({
    width: 320,
    height: 240
});
```

補充說明，如果是非必要引數，在建立好物件後使用 Setter 做設置也是一個方法。

