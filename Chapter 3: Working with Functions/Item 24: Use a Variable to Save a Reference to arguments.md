# 條款 24 使用一個變數來儲存對 arguments的參考(Reference)

<<<<<<< HEAD

**每個函式的主體 (body) 中都會有一個新的 arguments 變數被隱含地繫結**。我們感興趣的 arguments 物件是關聯到 values 函式的那一個，**但這個 iterator 的 next 方法含有它自己的 arguments 變數**，所以當我們回傳 arguments[i++] 時，我們是存取 it.next 的一個引數，而非 values 的引數。


```javascript
function values() {
  var i = 0, n = arguments.length; 
  return {
    hasNext: function() { 
      return i < n;
    },
    next: function() {
      if (i >= n) {
        throw new Error("end of iteration");
      }
      return arguments[i++];
    }
  };
}

var it = values(1, 4, 1, 4, 2, 1, 3, 5, 6); 

it.next(); // undefined
it.next(); // undefined
it.next(); // undefined
```

```javascript
function values() {
  var i = 0, n = arguments.length, a = arguments; // 閉包
  return {
    hasNext: function() { 
      return i < n;
    },
    next: function() {
      if (i >= n) {
        throw new Error("end of iteration");
      }
      return a[i++]; // 使用 arguments[i++] 查找會失效
    }
  };
}

var it = values(1, 4, 1, 4, 2, 1, 3, 5, 6); 

it.next(); // 1
it.next(); // 4
it.next(); // 1
```

> 參考到 arguments 時，請注意函式嵌套的層級 (nesting level)。
> 將一個範疇明確的參考 (reference) 繫結到 arguments 以便從巢狀的函式 (nested functions) 內也能夠參考它。
=======
## 使用 generator

```javascript
function* values () {
  let i = 0;
  while (i < arguments.length) {
    yield arguments[i++]; // wrong arguments
  }
}
```

執行

```javascript
it.next().value // 1
it.next().value // 4
it.next().value // 1
it.next().value // 4
it.next().value // 2
it.next().value // 1
it.next().value // 3
it.next().value // 5
it.next().value // 6
it.next().value // undefined
```

想要寫一個一樣的東西，怎麼寫呢？

## 用一般的 function 

```javascript
function values () {
  let i = 0;
  let n = arguments.length;
  return {
    next: function () {
      if (i >= n) {
        throw new Error("end of iteration");
      }
      return {
        value: arguments[i++], // wrong arguments 
        done: i < n
      }
    }
  };
}
```

得到結果，value 卻已經 undefined

```javascript
it.next();
// {value: undefined, done: true}
```

## 用 `...arg`

```javascript
function values () {
  let i = 0;
  let n = arguments.length;
  let a = arguments;
  return {
    next: function () {
      if (i >= n) {
        throw new Error("end of iteration");
      }
      return {
        value: a[i++], // right arguments 
        done: i < n
      }
    }
  };
}
```

得到想要的結果。

```javascript
it.next(); // {value: 1, done: true}
it.next(); // {value: 4, done: true}
it.next(); // {value: 1, done: true}
it.next(); // {value: 4, done: true}
it.next(); // {value: 2, done: true}
it.next(); // {value: 1, done: true}
it.next(); // {value: 3, done: true}
it.next(); // {value: 5, done: true}
it.next(); // {value: 6, done: false}
it.next();
// Uncaught Error: end of iteration
//     at Object.next (<anonymous>:8:15)
//     at <anonymous>:1:4
```

## 用 `...arg`

```javascript
function values (...args) {
  let i = 0;
  let n = args.length;
  return {
    next: function () {
      if (i >= n) {
        throw new Error("end of iteration");
      }
      return {
        value: args[i++],
        done: i < n
      }
    }
  };
}
```

得到想要的結果。

```javascript
it.next(); // {value: 1, done: true}
it.next(); // {value: 4, done: true}
it.next(); // {value: 1, done: true}
it.next(); // {value: 4, done: true}
it.next(); // {value: 2, done: true}
it.next(); // {value: 1, done: true}
it.next(); // {value: 3, done: true}
it.next(); // {value: 5, done: true}
it.next(); // {value: 6, done: false}
it.next();
// Uncaught Error: end of iteration
//     at Object.next (<anonymous>:8:15)
//     at <anonymous>:1:4
```
>>>>>>> 9f77042b08983322cd51ed84dcd089d575bb949e
