# 條款 08 盡量少用全域物件

## 可能導致的命名衝突
  全域變數在不同的作用域中計算，互相污染，導致結果值不如預期：
  ```javascript
  var i, sum;  //全域變數
  var amountArray = [
    [2, 2, 4],
    [5, 1, 2],
    [3, 1, 4]
  ];
  
  const totalAmount = total();
  console.log(totalAmount);   // 8

  function total() {
    sum = 0;
    for(i=0 ; i<amountArray.length ; i++) {
      sum += part(amountArray[i]);
      console.log(`i 目前的值為 ${i}, 總計算結果為： ${ sum }`);
    }
    return sum;
  }

  function part(item) {
    sum = 0;
    for(i=0 ; i<item.length ; i++) {
      sum += item[i];
      console.log(`第${i + 1}次計算結果為： ${ sum }`);
    }
    return sum;
  }
  ```

## 盡量將變數區域化
```javascript
var amountArray = [
    [2, 2, 4],
    [5, 1, 2],
    [3, 1, 4]
  ];
  
const totalAmount = total();
console.log(totalAmount);

function total() {
  var i, sum;  //全域變數
  sum = 0;
  for(i=0 ; i<amountArray.length ; i++) {
    sum += part(amountArray[i]);
    console.log(`i 目前的值為 ${i}, 總計算結果為： ${ sum }`);
  }
  return sum;
}

function part(item) {
  var i, sum;  //全域變數
  sum = 0;
  for(i=0 ; i<item.length ; i++) {
    sum += item[i];
    console.log(`第${i + 1}次計算結果為： ${ sum }`);
  }
  return sum;
}
```
## 功能偵測的應用
透過 this，可以查看宿主環境是否有提供該功能：
:::info
宿主環境為：
- 瀏覽器，this 為 window
- node.js，this 為 global
:::
```javascript
// polyfill 查看宿主環境有沒有提供 JSON 物件
// 如果沒有就在全域物件新增 JSON 物件，並寫入屬性
if(!this.JSON) {
  this.JSON = {
    parse: ...,
    stringify: ...
  }
}
```
## Question:
- 未繫結變數是什麼？
- 用 `var` 宣告的全域變數跟綁在全域物件上的變數，以及沒有用 var 宣告的變數，有差嗎？

建立全域變數很簡單，像這樣子：

```javascript
i = 0;
```

然後 i 就會被加到全域裡了 `gobal`，然後 `gobal` 這個空間是由一個 **全域物件** 被釋出，作為 `this` 的初始值，也會被繫結到全域的 `window` 變數（所以 `window` 在 `this` 下，但用 `this` 又可以存取到 `window`）：

```javascript
i = 0;
window.i;
this.i;
// 上方都會存取到全域 i

// 下方都會存取到 window 物件
this;
this.window;
```

**全域變數會汙染所有人共用的命名空間**，因為是所有人共有的嘛，所以可能導致名稱衝突：

```javascript
// 貼心小提醒，此範例會進入無窮迴圈，因為共用了全域變數 i

function doSomething() {
  i = 0;
  return i;
}

function aLoop() {
  for(i = 0; i <= 5; i += 1) {
    console.log(doSomething());
  }
}
```

上方的程式只要把變數放到 function 裡用 `var`、`let`、`const` 等宣告，`i`就不會是全域了：

```javascript
// 放心試吧，每一個 i 都在各自的作用域中被宣告

function doSomething() {
  const i = 0;
  return i;
}

function aLoop() {
  for(let i = 0; i <= 5; i += 1) {
    console.log(doSomething());
  }
}
```

## 可以用全域物件的地方

「動態反思（dynamically reflect）」，個人是沒查到 dynamically reflect 這個詞和 JavaScript 的關係（[查詢結果](https://www.google.com/search?sxsrf=ALeKk03rb9so8h7JXpoGpT0uG1X0XB9W3w%3A1594894895450&ei=LyoQX9SQG5KVr7wP2dS4-AM&q=dynamically+reflect+javascript&oq=dynamically+reflect+javascript&gs_lcp=CgZwc3ktYWIQAzIGCAAQDRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEAgQDRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEA0QBRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEAgQDRAeOgQIIxAnOgYIABAIEB46BQghEKABUOoHWN8YYM4ZaABwAHgAgAHqAYgBrQmSAQU5LjEuMpgBAKABAaoBB2d3cy13aXo&sclient=psy-ab&ved=0ahUKEwiU5Mm9xtHqAhWSyosBHVkqDj8Q4dUDCAw&uact=5)），可能是作者自己想的，不過他的意思應該是，直接從 `this` 或 `window` 去找自己想要的東西有沒有存在，如果沒有的話就自己加上去，以避免每個瀏覽器對某語法的支援度不同，導致程式出錯，範例是：

```javascript
// 如果 this 沒有有關 JSON 的 methods
if (!this.JSON) {
  // 就自己寫吧
  this.JSON = {
    parse: () => { /* ... */ },
    stringify: () => { /* ... */ },
  };
}
```

