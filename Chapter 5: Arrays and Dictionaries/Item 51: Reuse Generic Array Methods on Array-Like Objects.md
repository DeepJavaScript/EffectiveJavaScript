# 條款 51 在類陣列物件上重複使用一般化的 Array 方法

新標準中 array-like 已經有，而且只有 forEach 這個 method

新的寫法有幾個推薦的做法

## 不使用 argumrnts 

arguments 已經被新標準棄用

## 使用 `...` 將 arguments 轉成 Array

```javascript
function myFun () {
  console.log([...arguments] instanceof Array)
}

myFun(1)        // true
myFun(1, 2)     // true
myFun(1, 2, 3)  // true
```

## 在函數參數使用 `...` 來處理

使用這種方法，可以捕捉到任意長度的引數 (arguments) 並且是 Array 的型別。
可以直接使用 Array methods

```javascript
function myFun (...myArguments) {
  console.log(myArguments instanceof Array)
}

myFun(1)        // true
myFun(1, 2)     // true
myFun(1, 2, 3)  // true
```

除了函式裡的 `arguments` 是類陣列之外，使用 `document.getElementsByTagName` 等方法取得的 Node List，也是類陣列。

