# 條款 02 了解 JavaScript 的浮點數字

## Javascript 的數字特性

多數的語言，都有很多種數字型別，JavaScript，只有一種: `number` 型別，其實都是 `double-precision` 數字 ([IEEE 754](https://zh.wikipedia.org/wiki/IEEE_754) 的 64 位元數字)。

**string to number**

- `parseInt()`
- `parseFloat()`

**number to string**

- `3 .toString()`
- `3 .toString(2)` // 轉二進制

## 整數

在 double 中的整數，有效表示範圍
-9007199254740992 (-2^53) 到 9007199254740992 (2^53)

## 32 位元 `signed integer` 的位元運算

位元的算術運算子包括: 
- 位元運算運算子: `~`、`&`、`|`、`^`
- 位移運算子 (shift operator): `<<`、`>>`、`>>>`

計算時不會把引數 (arguments) 當浮點數來運算，而會視為 32 位元的 (big-endian) 二補數整數。

**例如: 8 | 1 = 9**

```javascript
8 | 1;  // 9
```

**計算過程很像這樣:**

- 將 8 轉為二進位 (32 位元): `00000000000000000000000000001000`
- 將 1 轉為二進位 (32 位元): `00000000000000000000000000000001`

進行 OR 運算後會變成: `00000000000000000000000000001001`

將 `1001` 轉成 10 進位: 

```javascript
parseInt('1001', 2);  // 9
```

**例如: 8888888888 經過位元運算變 298954296**

另一個範例: 為何 `8888888888 | 1` 會等於 `298954297`？

```javascript
298954296 | 1;  // 298954297
```

因為`8888888888` 轉為二進位是 `1000010001110100011010111000111000` (34 位元): 

```javascript
(8888888888).toString(2);  // "1000010001110100011010111000111000"
"1000010001110100011010111000111000".length;  // 34
```

但位元運算只會用到 32 位元，所以當 `8888888888 | 1` 運算時，就會去掉多餘的位元，像 `8888888888` 的二進位 (因有 34 位元，會被去掉兩個位元) 最前面的 `10` 這兩個位元就會被去掉，所以實際上位元運算是這樣: 

```javascript
parseInt("00010001110100011010111000111000", 2);  // 298954296

298954296 | 1;  // 298954297
```

## 浮點數

### 浮點誤差

- 浮點數運算，如果超過 64 位元的話會有捨入誤差。

```javascript
0.1 + 0.2 = 0.30000000000000004;
0.1 + 0.2 === 0.3;  // false
```

一般來說，(x+y)+z = x+(y+z)，但 JS 的浮點數並不一定: 

```javascript
(0.1 + 0.2) + 0.3; // 0.6000000000000001
0.1 + (0.2 + 0.3); // 0.6
```

### 避免方式浮點誤差

**運用整數做運算**

```javascript
const caryOver = 10;
if ((0.1 * caryOver + 0.2 * caryOver)/caryOver === 0.3) //true
```

```javascript
(10 + 20) + 30; // 60
10 + (20 + 30); // 60
```

**運用夾擊判斷**

```javascript
const value = 0.1 + 0.2;
value // 0.30000000000000004
if (0.2999999 < value && value < 0.3000001)
```

**運用 JavaScript 的可忽略誤差: `Number.EPSILON`**

```javascript
function numbersCloseEnoughToEqual(n1,n2) {
	return Math.abs( n1 - n2 ) < Number.EPSILON;
}
```

```javascript
var a = 0.1 + 0.2;
var b = 0.3;
numbersCloseEnoughToEqual( a, b );	// true
numbersCloseEnoughToEqual( 0.0000001, 0.0000002 );
```

```javascript
numbersCloseEnoughToEqual((0.1 + 0.2) + 0.3, 0.6)
```
