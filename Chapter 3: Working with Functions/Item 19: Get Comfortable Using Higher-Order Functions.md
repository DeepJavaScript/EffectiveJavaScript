# 條款 19 熟悉高階函式的用法

## 重點整理

1. 認識 heigher-order function 定義 (二擇一)
   1. 把函式當作引數，並在內部執行引數函數。 
   2. 回傳函式作為回傳值。
2. 熟悉程式庫中提供的高階函式
3. 使用 heigher-order function 取代重複性高的程式碼，並賦予意義
   1. 函式內容有些重複，可以將不重複的部份抽離成 callback
   2. heigher-order function 的回傳值可以由 callback 回傳值組成。

## 認識 heigher-order function 定義

heigher-order functions 過去 functional programming 僧侶們的特有用語 (shibboleth)，看起來像是一種高階程式技巧的深奧術語，但事實並非如此

只是會做到這兩件事而已

1. 把函式當作引數，並在內部執行引數函數。 
2. 回傳函式作為回傳值。

ex:

`Array#sort` 就是一種 heigher-order function

```javascript
[3, 1, 4, 1, 5, 9].sort((x, y) => {
  if (x < y) {
    return -1;
  }
  if (x > y) {
    return 1;
  }
  return 0;
}); // [1, 1, 3, 4, 5, 9]
```

## 熟悉程式庫中提供的高階函式

常用的地方: 

- Array methods 都算是 heigher-order function

```javascript
var names = ["Fred", "Wilma", "Pebbles"];
var upper = [];
for (var i = 0, n = names.length; i < n; i++) {
  upper[i] = names[i].toUpperCase();
}
upper; // ["FRED", "WILMA", "PEBBLES"]
```

可以改用 `Array#map`

```javascript
["Fred", "Wilma", "Pebbles"].map(name => name.toUpperCase());
// ["FRED", "WILMA", "PEBBLES"]
```



## 使用 heigher-order function 取代重複性高的程式碼，並賦予意義

整理 heigher-order function 的心法

- 函式內容有些重複，可以將不重複的部份抽離成 callback

產生 26 個字母

```javascript
var aIndex = "a".charCodeAt(0); // 97
var alphabet = "";
for (var i = 0; i < 26; i++) {
   alphabet += String.fromCharCode(aIndex + i); 
}
alphabet; // "abcdefghijklmnopqrstuvwxyz"
```

產生含有數字的字串

```javascript
var digits = "";
for (let i = 0; i < 10; i++) {
  digits += i;
}
digits; // "0123456789"
```

產生隨機字串

```javascript
var random = "";
for (let i = 0; i < 8; i++) {
  random += String.fromCharCode(Math.floor(Math.random() * 26) + aIndex);
}
random; // "bdwvfrtp" (different result each time)
```

三種功能都不一樣。又要如何共用程式碼呢？
以 height-order function 可以共用 function 的某一段邏輯，在此可以共用的部份
   
這個例子就像是 Array methods 的設計過程，將邏輯與迴圈控制分離，可以在開發時專注的撰寫邏輯，不受迴圈控制的 bug 干擾思緒，最後回傳值，是由迭代過程累積的字串。
為這個 height-order function 命名為 buildString 有助於程式碼可讀性

```javascript
function buildString (n, callback) {
  let result = "";
  for (let i = 0; i < n; i++) {
    result += callback(i);
  }
  return result;
}
```

改寫三個函數如下

```javascript
var alphabet = buildString(26, i => String.fromCharCode(aIndex + i));
alphabet; // "abcdefghijklmnopqrstuvwxyz"
```

```javascript
var digits = buildString(10, i => i); 
digits; // "0123456789"
```

```javascript
var random = buildString(8, () => String.fromCharCode(Math.floor(Math.random() * 26)
+ aIndex))
random; // "ltvisfjr" (different result each time)
```
