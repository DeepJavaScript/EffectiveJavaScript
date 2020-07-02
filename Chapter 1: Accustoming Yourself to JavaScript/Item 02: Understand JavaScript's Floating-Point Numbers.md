# 條款 02 了解 JavaScript 的浮點數字

多數的語言，都有很多種數字型別。
JavaScript，只有一種: `number`，包含了`integers`和`floating-point number`

事實上 JavaScript 中所有的數字都是 `double-precision floating-point` [IEEE 754](https://zh.wikipedia.org/wiki/IEEE_754) 的 64位元數字

### 條款 02 了解 Javascript 的浮點數字
#### Javascript 的數字特性
- 用 `typeof` 檢查，整數與浮點數都是同一個 number 型別。
- 是由 IEEE 754 規範的 64 位元數字編碼（俗稱 double）。
- 整數最精準可以運算至 53 位元。
- 位元運算子是將值作為 32 位元的二進位運算。
    ```javascript
    (8).toString(2);      // '1000'
    (1).toString(2);      // '0001'
    
    8 | 1;                // 1001 => 9
    parseInt('1001', 2);  // 9
    ```
- 浮點數運算，如果超過 64 位元的話會有捨入誤差。
    ```javascript
    0.1 + 0.2 = 0.30000000000000004;
    0.1 + 0.2 === 0.3;  // false
    ```
    > 盡量運用整數做運算可以避免誤差：
    ```javascript
    const caryOver = 10;

    (0.1 * caryOver + 0.2 * caryOver)/caryOver === 0.3 //true