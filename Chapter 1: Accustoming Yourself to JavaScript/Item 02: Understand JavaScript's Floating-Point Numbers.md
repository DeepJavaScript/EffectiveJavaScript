# 條款 02 了解 JavaScript 的浮點數字

多數的語言，都有很多種數字型別。
JavaScript，只有一種: `number`，包含了`integers`和`floating-point number`

事實上 JavaScript 中所有的數字都是 `double-precision floating-point` [IEEE 754](https://zh.wikipedia.org/wiki/IEEE_754) 的 64 位元數字

### 條款 02 了解 Javascript 的浮點數字

#### Javascript 的數字特性

- 用 `typeof` 檢查，整數與浮點數都是同一個 number 型別。
- 是由 IEEE 754 規範的 64 位元數字編碼（俗稱 double）。
- 整數最精準可以運算至 53 位元。
- 位元運算子是將值作為 32 位元的二進位運算。
  ```javascript
  (8).toString(2); // '1000'
  (1).toString(2); // '0001'

  8 | 1; // 1001 => 9
  parseInt("1001", 2); // 9
  ```
- 浮點數運算，如果超過 64 位元的話會有捨入誤差。

  ```javascript
  0.1 + 0.2 = 0.30000000000000004;
  0.1 + 0.2 === 0.3; // false
  ```

  > 盡量運用整數做運算可以避免誤差：

  ```javascript
  const caryOver = 10;

  (0.1 * caryOver + 0.2 * caryOver) / caryOver === 0.3; //true
  ```

很多程式語言都有至少兩種數字型別，像是 pyhton 用 int 表示整數，float 表示浮點數，但在 JS 中只有一種：數字(number)。

一般情況下，大部分的算數運算子可以自由組合計算整數或小數，但位元運算子(bitwise arithmetic operators)的計算方式比較特殊，JS 會將數字轉換成 32 位元的二進制後才計算，例如：

```
5 | 8 = 13
(5).toString(2) = 101
(8).toString(2) = 1000
parseInt("1101",2) = 13
```

## 常見的浮點數問題

```
0.1+0.2; //0.30000000000000004
```

由于 JS 中浮點數只有 53 位的精度，導致數字轉成二進制時，後面的位數會被捨棄，所以計算完成轉成十進制時就會出現誤差。

一般來說，(x+y)+z = x+(y+z)，但 JS 的浮點數並不一定：

```
(0.1 + 0.2) + 0.3; // 0.6000000000000001
0.1 + (0.2 + 0.3); // 0.6
```

解決方式：
盡量使用範圍在 –2 ^53 和 2 ^53 之間的整數進行計算，然後再處理放大倍數

## 總結

1、JS 的數字都是 double-precision floating-point，整數是其中一個子集，不是單獨的資料型別
2、位元運算子將數字視為 32 bits 的有符號整數
3、小心浮點數計算的精度問題
