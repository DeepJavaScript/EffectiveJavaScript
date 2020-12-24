# 條款 47 不要把可列舉的特性加到 Object.prototype 中

`for in` 迴圈可以把 enumeration，容易受 prototype 污染的影響

真的要加的話，請使用 ES5 的 `Object.defineProperty`

```javascript
Object.defineProperty(object1, 'property1', {
  //...
  enumerable: false
});
```

