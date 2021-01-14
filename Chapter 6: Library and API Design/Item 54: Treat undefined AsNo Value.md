# 條款 54 把 undefined 視為「沒有值」

`undefined` 是在 JavaScript 沒有特定值可以提供的時候回傳，例如以下幾種情況：

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