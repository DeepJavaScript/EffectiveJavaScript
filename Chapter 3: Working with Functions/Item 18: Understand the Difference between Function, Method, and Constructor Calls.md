# 條款 18 了解函式呼叫、方法呼叫以及建構式呼叫之間的差異

**戡誤**

    Item 18
    Page 57, Paragraph 4: "binds the name parameter" should be "binds the username parameter"
    Page 59, Paragraph 1, 1st Sentence: "has no property called name" should be "has no property called username". (reported by Stein Magnus Jodal)

> 在 JavaScript 中，單是函式這個功能，就能夠扮演其他程式語言以多個不同名稱實現的角色，程序(procedures)、方法(methods)、建構式(constructors)，甚至是類別(classes)或模組(modules)。一旦你熟悉函式豐富又細膩之處，並且能夠熟練地運用它們，你就已經精通了絕大部分的 Javascript。

❓ 疑惑其他語言的狀況？

- 程序(procedures)：一般的 function
- 方法(methods)：物件裡的 function，通常會含有 this
- 建構式(constructors)：用 new 產生藍圖的過程，通常為 new + 類別名稱
- 類別(classes)：物件的藍圖，通常語言會設計成類別的名字，也就為 constructors 的名字
- 模組(modules)：檔案層級

以上述五個功能來說，都是各自獨立的功能，但 JS 可以統一以函式來理解處理。
以 class 中的 constructors 為例，其他語言的 constructors 是黑盒子，沒辦法看到內部情況，但 JS 的 constructors 就是函式，可以自訂內容。

---

## 函式、方法與類別在 JS 中是函式的三種使用方式

### 用途一：函式呼叫

```javascript
function hello(username) {
  return "hello, " + username;
}
hello("Keyser Söze"); // "hello, Keyser Söze"
```

- 呼叫 hello 的時候，將 username 繫結到傳入給它的參數

---

### 用途二：方法

```javascript
var obj2 ={
  hello:function(){
    return "hello," + this.username;
  },
  username: "Hans Gruber"
}
obj2.hello(); //"hello,Hans Gruber"
```
- 對一個方法呼叫而言，實際上是由呼叫運算式本身負責決定 this 的繫結，而 this 也被稱為該次呼叫的接收者。

```javascript
var obj1 ={
  hello:function(){
    return "hello," + this.username;
  },
  username: "Han Gru"
}

var obj2 ={
  hello:obj1.hello,
  username: "Hans Gruber"
}
obj2.hello(); //"hello,Hans Gruber"
```

- obj2.hello() 會去查找 obj2 的 hello 特性(在此這個特性剛好是與 obj1.hello 相同的函式)，不過是以 obj2 為接收者來呼叫他
- 在一個物件上呼叫一個方法，就會在那個物件上查找那個方法，然後使用該物件作為方法的接收者。

那麼接下來讓一個普通函式提及 this，並讓多個物件共用它

```javascript
function hello() {
  return "hello, " + this.username;
}

var obj1 = {
  hello: hello,
  username: "Gordon Gekko"
};
obj1.hello(); // "hello, Gordon Gekko"

var obj2 = {
  hello: hello,
  username: "Biff Tannen"
  };
obj2.hello(); // "hello, Biff Tannen"
```

但一個非方法的函式呼叫，會把全域當作接收者，是一個不好的做法

```javascript
hello() // "hello,undefined"
```

- 如果一個方法的效用取決於 this，那麼把它當函式呼叫，很少會有用處，因為沒有理由預期全域物件會符合該方法對它接收者物件的期待(以上述為例，無法期待全域有 username )
- ES5 strict 模式會把 this 的預設繫結設為 undefined

```javascript
function hello() {
  "use strict";
  return "hello, " + this.username;
  }
hello(); // error: cannot read property "username" of undefined
```

---

### 用途三：建構式
- 建構式一樣使用 function 來定義，名稱上一般會以首字大寫來定義

```javascript
function User(name, passwordHash) {
  this.name = name;
  this.passwordHash = passwordHash;
}
```

- 建構式需要使用 new 來調用

```javascript
var u = new User("sfalken", "0ef33ae791068ec64b502d6cb0191387");
u.name; // "sfalken"
```

- 建構式呼叫會以一個全新的物件當作 this 的值，並隱含的回傳這個新物件當做呼叫結果
- 建構函式的主要用途就是初始化那個新物件
