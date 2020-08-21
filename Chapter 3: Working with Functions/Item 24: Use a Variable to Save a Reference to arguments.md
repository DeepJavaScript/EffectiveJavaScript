# 條款 24 使用一個變數來儲存對 arguments的參考(Reference)

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
