# 條款 24 使用一個變數來儲存對 arguments的參考(Reference)


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