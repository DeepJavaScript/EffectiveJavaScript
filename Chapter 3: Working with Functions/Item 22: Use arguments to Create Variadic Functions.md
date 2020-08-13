# 條款 22 使用 arguments 來建立可變參數的函式

### 函式形式參數數量是固定的
當我們聲明函式，會在括號內，訂定我們的形式參數數量：
```javascript
function averageThreeNumber(one, two, three) {
  let sum = 0;
  sum = (one + two + three) / 3;
  return sum;
}

averageThreeNumber(1, 2, 3);  // 2
```
如果我們呼叫的時候，引入超過形式參數數量的引數，那些多出來的引數會被忽略掉：

```javascript
averageThreeNumber(1, 2, 3, 4, 5);  // 2
```
### 透過陣列的特性達到可變參數函式
自己實作一個可變參數函式（veriadic function），就可以算出數量不等的數字平均值。
以下例子為元數固定的例子（只能傳進一個參數），但利用陣列傳入，並計算陣列內所有值的平均值，便可達到介面彈性的效果。

```javascript
function averageOfArray(arr) {
  for(var i = 0, sum = 0; i < arr.length; i++) {
    sum += arr[i];
  }
  return sum / arr.length;
}

averageOfArray([1, 2, 3, 4, 5]);  // 3
averageOfArray(1 , 2, 3, 4, 5);   // NaN
```
### arguments 的類陣列特性

原生的 JavaScript 有提供 `arguments` 來建立可變參數的函式。
- `arguments` 是一個隱含的參數，會在呼叫的時候，接住所有引數。
- `arguments` 是一個類陣列，它本身有索引 (index) 跟長度 (length) 的特性。
  可是因為不是一個真正的陣列，無法使用其它 Array Methods (例如 map、reduce、filter...)。

利用這樣的特性，我們可以透過它來達到可變元數函式的效果：

```javascript
function average() {
  for(var i = 0, sum = 0; i < arguments.length; i++){
    sum += arguments[i];        
  }
  return sum / arguments.length;
}

average([1, 2, 3, 4, 5]);  // NaN
average(1, 2, 3, 4, 5);    // 3
```
### ...args 剩餘參數的特性

原生 JavaScript 也有剩餘參數可利用，在註冊函式時，用剩餘參數可以接著呼叫時數量不等的引數。
- `...args` 是陣列，因此陣列的方法它都有繼承到，都可使用。
- `...args` 可以是單獨行參，也可以在前面放數量不等的形式參數。
- `...args` 應該作為最後一個參數。

```javascript
function avergeChagebleArg(...args) {
  for(var i = 0, sum = 0; i < args.length; i++){
    sum += args[i];        
  }
  return sum / args.length;
}

avergeChagebleArg(1, 2, 3, 4, 5);
```

### 各自的限制

以上兩種方法，都可以達到介面的彈性，讓使用者傳入任意的參數數量，可是它們各自也有限制。
- `averageOfArray` 元數固定，如果不是用陣列的方式傳入，就會計算失敗
- `average` 元數不固定，可是因為 `arguments` 的特性，它會變成二維的類陣列，也會計算失敗。

要解決這個限制，可以像 item 21 的範例，用 `apply` 的特性來達到效果。或者結合兩者作法，提供一個元數可變的函式：

```javascript
function average() {
  return averageOfArray(arguments);
}

function averageOfArray(arr) {
  for(var i = 0, sum = 0; i < arr.length; i++) {
    sum += arr[i];
  }
  return sum / arr.length;
}

average(1, 2, 3, 4, 5);   // 3
average([1, 2, 3, 4, 5]); // 還是 NaN 啊...無法兩者兼顧
```
### 重點整理
- 使用隱含的 `arguments` 參數來實作可變元數函式 (veriable-arity function)
- 提供可變參數函式時，也一併提供元數固定的版本，就可以不必使用 `apply` 方法。
- 盡可能使用 `...args` 剩餘參數，`arguments` 該被廢棄了。
- Arrow Function 沒有 arguments。

