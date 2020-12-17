# 條款 20 使用 call 以自訂的接收者來呼叫方法

method 的 this 是呼叫 method 的物件。
但是我們有時需要自定義某個函式(包含 function/method) 的 this，指定成我們想要的物件。
但是該物件的屬性並不擁有這個函式，無法直接使用。
若想讓該物件暫時使用這個 function，有下面兩種方法

**方法一: (不推薦)**

在該物件中，加上這個方法

```javascript
obj.tempMethod = fn;
obj.tempMethod(arg1, arg2);
delete obj.tempMethod;
```

限制: 物件可能被 frozen 或 sealed，新特性就無法加入了。

圖片摘自：Chris 技術筆記
![](https://i.imgur.com/cBBzJUj.png)

**方法二:**

使用函式方法 call

```javascript
f.call(obj, arg1, arg2)
```

類似

```javascript
f(obj, arg1, arg2)
```

第一個參數就是指定 this 的位置。

call 方法特別適合用來呼叫已經被移除、修改或覆寫的方法。
以 hasOwnProperty 為例，hasOwnProperty 方法可在任意物件上呼叫

```javascript
dict.hasOwnProperty("foo");//false
dict.hasOwnProperty = 1 //覆寫 hasOwnProperty
dict.hasOwnProperty("foo"); //錯誤，1 不是一個 function

//使用 call 方法呼，讓 dict 物件暫時呼叫 hasOwnProperty 方法
{}.hasOwnProperty.call(dict, "foo");//false
{}.hasOwnProperty.call(dict, "hasOwnProperty");//true
```

即使這個方法從該物件被移除，也可以用 call 方法呼叫
```javascript
//書中範例，覺得有點繞圈子
dict.hasOwnProperty = 1
var hasOwnProperty = {}.hasOwnProperty;
dict.foo = 1;
delete dict.hasOwnProperty;
hasOwnProperty.call(dict, "foo");//true
hasOwnProperty.call(dict, "hasOwnProperty");//false


//改寫如下
dict.hasOwnProperty = 1
dict.foo = 1;
delete dict.hasOwnProperty;
{}.hasOwnProperty.call(dict, "foo");//true
{}.hasOwnProperty.call(dict, "hasOwnProperty");//false

```


## 超展開討論

```javascript=
class MyArguments extends Array {
  constructor(arugmemt) {
    super(...arugmemt);
  }
}

function foo() {
  const args = new MyArguments(arguments);
  return args;
}

let ary = foo(1, 2);
ary.map(x => x + 1)
//TypeError: Found non-callable @@iterator
```

```javascript=
class MyArguments extends Array {
  constructor(arugmemt) {
    super(Array.prototype.slice(arugmemt));
  }
}

function foo() {
  return new MyArguments(arguments);
}

let ary = foo(1, 2);
ary.map(x => x + 1)
// MyArguments ["1"]
```

```javascript=
class MyArguments extends Array {
  constructor(...arugmemt) {
    super(arugmemt);
  }
}

function foo() {
  const args = new MyArguments(...arguments);
  return args;
}

let ary = foo(1, 2);
ary.map(x => x + 1)
//MyArguments ["1,21"]
```

```javascript=
class MyArguments extends Array {
  constructor(arugmemt) {
    super(...arugmemt);
  }
}

function foo() {
  const args = new MyArguments(arguments);//因為 arguments 不是 iterable
  return args;
}

let ary = foo(1, 2);
ary.map(x => x + 1)
//TypeError: Found non-callable @@iterator
```


```javascript=
class MyArguments extends Array {
  constructor(arugmemt) {
    super(...arugmemt);
    this[Symbol.iterator] = function () { return this }
  }
    
}

function foo() {
  return new MyArguments(arguments);
}

let ary = foo(1, 2);
ary.map(x=>x+1)
//TypeError: Found non-callable @@iterator
```


可繼承 Array
**方法一:**
```javascript=
class MyArguments extends Array {
  constructor(argument) {
    super(...Array.from(argument));
  }
}

function foo() {
  const args = new MyArguments(arguments);
  return args;
}

let ary = foo(1, 2);
ary.map(x => x + 1)
```
**方法二:**
```javascript=
function foo() {
  const args = Array.from(arguments);
  return args;
}
let ary = foo(1, 2);
ary.map(x=>x+1)
```