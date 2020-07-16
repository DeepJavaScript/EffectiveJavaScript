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
console.log(totalAmount);   // 8

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