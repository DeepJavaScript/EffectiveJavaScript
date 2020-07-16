# 條款 08 盡量少用全域物件

## 可能導致的命名衝突
  全域變數在不同的作用域中計算，互相污染，導致結果值不如預期：
  ```javascript
  var i, sum;  //全域變數
  var amountArray = [
    [2, 2, 4],
    [5, 1, 2],
    [3, 1, 4]
  ];
  
  const totalAmount = total();
  console.log(totalAmount);   // 8

  function total() {
    sum = 0;
    for(i=0 ; i<amountArray.length ; i++) {
      sum += part(amountArray[i]);
      console.log(`i 目前的值為 ${i}, 總計算結果為： ${ sum }`);
    }
    return sum;
  }

  function part(item) {
    sum = 0;
    for(i=0 ; i<item.length ; i++) {
      sum += item[i];
      console.log(`第${i + 1}次計算結果為： ${ sum }`);
    }
    return sum;
  }
  ```

## 盡量將變數區域化
```javascript
var amountArray = [
    [2, 2, 4],
    [5, 1, 2],
    [3, 1, 4]
  ];
  
const totalAmount = total();
console.log(totalAmount);

function total() {
  var i, sum;  //全域變數
  sum = 0;
  for(i=0 ; i<amountArray.length ; i++) {
    sum += part(amountArray[i]);
    console.log(`i 目前的值為 ${i}, 總計算結果為： ${ sum }`);
  }
  return sum;
}

function part(item) {
  var i, sum;  //全域變數
  sum = 0;
  for(i=0 ; i<item.length ; i++) {
    sum += item[i];
    console.log(`第${i + 1}次計算結果為： ${ sum }`);
  }
  return sum;
}
```
## 功能偵測的應用
透過 this，可以查看宿主環境是否有提供該功能：
:::info
宿主環境為：
- 瀏覽器，this 為 window
- node.js，this 為 global
:::
```javascript
// polyfill 查看宿主環境有沒有提供 JSON 物件
// 如果沒有就在全域物件新增 JSON 物件，並寫入屬性
if(!this.JSON) {
  this.JSON = {
    parse: ...,
    stringify: ...
  }
}
```
## Question:
- 未繫結變數是什麼？
- 用 `var` 宣告的全域變數跟綁在全域物件上的變數，以及沒有用 var 宣告的變數，有差嗎？