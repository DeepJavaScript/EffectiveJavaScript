# 條款 18 了解函式呼叫、方法呼叫以及建構式呼叫之間的差異
> 在 JavaScript 中，單是函式這個功能，就能夠扮演其他程式語言以多個不同名稱實現的角色，程序(procedures)、方法(methods)、建構式(constructors)，甚至是類別(classes)或模組(modules)。一旦你熟悉函式豐富又細膩之處，並且能夠熟練地運用它們，你就已經精通了絕大部分的 Javascript。

❓ 疑惑其他語言的狀況？


## 函式、方法與類別在 JS 中是函式的三種使用方式
- 1. 函式呼叫
```javascript
function hello(username) { 
  return "hello, " + username;
}
hello("Keyser Söze"); // "hello, Keyser Söze"
```
