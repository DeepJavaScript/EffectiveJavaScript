# 條款 26 使用 bind 建立 Curry 函式

## Curry Function 柯里函式

一個函式柯里化的過程就是**把接受多個參數的函式，變換成接受一個單一參數的函式，並且返回接受餘下的參數而且返回結果的新函式的技術。**

假設我們有一個計算兩數相加的函式，它長這樣子：
```javascript
const add = (a, b) => a + b;
```

柯理化過後會變成這樣子：
```javascript
const add = a => b => a + b;
```

這裡是沒有箭頭，然後可能比較好看懂的柯里化後的函式：
```javascript
function add(a) {
  return function(b) {
    return a + b;
  };
};
```

在柯里化之前，要使用 `add` 必須一次傳入兩個參數：

```javascript
add(2, 3);
```

但是根據上述柯里化過程的定義就是，要把傳入多個參數的函式，變成傳入單個，而剩下的參數再透過回傳的函式補齊，呼叫起來會變這樣子：

```javascript
add(2)(3);
```

上方的呼叫在執行過程會是：
1. 傳入第一個參數 2，`add` 會回傳一個函式。
2. 再對回傳的函式，傳入第二個參數 3 執行。
3. 執行的時候就會把 2 和 3 加起來。

優點是可以靈活定義函式的使用，以 `add` 的例子來說，如果未做柯里化之前，即使在當前的執行下，有個參數的值是固定的，你仍然得需要在每一次使用都傳入兩個參數：

```javascript
add(20, 85);
add(20, 65);
```

也許你可以這麼修改，來減少重複傳入 20 這個數字：

```javascript
const deliveryFee = 20;

add(deliveryFee, 85);
add(deliveryFee, 65);
```

上方的更改僅僅只是將 20 語意化而已，重複傳入本身是沒改變的，但如果是柯理化的函式，就可以這麼做：

```javascript
const addDeliveryFee = add(20);

addDeliveryFee(85);
addDeliveryFee(65);
```

利用柯理函式，就可以先**只定義部分功能**。

## 使用 bind 建立 Curry Function

現在知道柯里函式的優點是可以讓我們先**只定義部分功能，而只定義部分功能的原理是來自先定義某幾個函式需要的參數，先不去執行**。

如果使用 `bind` 的話，就能夠實現相同的功能，而不用把一般函式特別修改成柯里函式。因為 `bind` 會建立一個包裝原有函式的新函式（Bound Function）。舉例來說：

```javascript
const add = (a, b) => a + b;

// 透過 bind 產生一個新函式
const bindAdd = add.bind();
```

而 `bind` 能夠在綁定的時候給予幾個參數，第一個是 this，再來就是執行函式需要的參數，如果我們這麼做：

```javascript
const add = (a, b) => a + b;

const addDeliveryFee = add.bind(null, 20);
```

上方使用 `bind` 的時候，因為 `add` 不需要使用 this，所以直接送 null。第二個參數開始就會綁定到原本函式，也就是執行 `add` 的參數，因為我送了一個 20，所以 `add` 的 `a` 在新的 Bound Function 中就會被 `bind` 成 20。

如此一來，不需要修改 `add` 的內容，也能夠透過 `bind` 使用柯里函式的特性。

## Wrapper Functions 包覆函式

最後還提到了比包覆函式更簡潔，那就來看看如果用包覆函式寫相同功能會長怎樣吧：

```javascript
const add = (a, b) => a + b;

const addDeliveryFee = (b) => {
  const deliveryFee = 20;
  return add(20, b);
}
```

## 資料來源

1. Currying：https://zh.wikipedia.org/wiki/%E6%9F%AF%E9%87%8C%E5%8C%96
2. Bind：https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Function/bind
3. Wrapper Functions：https://www.cnblogs.com/rubylouvre/archive/2009/11/14/1602993.html


**戡誤**

    Item 26
    Page 75, 1st paragraph: The technique described is "partial application", not "currying".
