# 條款 54 把 undefined 視為「沒有值」

`undefined` 是在 JavaScript 沒有特定值可以提供的時候表示為一個沒有值，例如以下幾種情況：

1. 宣告變數但未指定值：
```javascript
const x;
console.log(x); // undefined
```
2. 存取 Object 不存在的 Properties：
```javascript
const x = {};
console.log(x.name); // undefined
```
3. Function 沒有回傳值：
```javascript
const func = () => {};
console.log(func()); // undefined
```
4. Function 在執行時沒有取得對應的引數：
```javascript
func();

const func = (a) => {
    console.log(a); // undefined
};
```

常被拿來比較的 `null` 其實是有意義的，`null` 的意義是初始的狀態值，和那種沒有適合的值才回傳的 `undefined` 是不一樣的。

要判斷 `undefined` 可以使用 `a === undefined` 或是 `(undefined || 'default value')` 來確認，但後者不是好方法，因為 true 或 false 都會影響到判斷，並不只針對 `undefined`。

> 補充：也可以使用 `typeof a === 'undefined'` 來判斷。
