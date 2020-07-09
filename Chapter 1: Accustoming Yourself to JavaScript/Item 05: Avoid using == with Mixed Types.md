# 條款 05 避免把==用在混合的型別上

`==` 是轉型比對
`===` 是不轉型比對

### 條款 05 避免把 `==` 用在混合的型別上

相等比較符：

- `==` 會強制轉型兩個引數，再做比較，應避免使用，除非開發者能夠熟背所有強制轉型的規則。
- `===`（不會強制轉型地比較兩個值） 是比較嚴謹的。
  > [Testing and Comparison Operations | read262](https://read262.netlify.app/abstract-operations/testing-and-comparison-operations#sec-abstract-equality-comparison)
  > 整理：
- 用 `==` 時，arg 的行為不同時會發生隱含的強制轉型
- 用 `===` 來表達這裡的程式碼不會發生隱含的強制轉型

![](https://i.imgur.com/kbNpnDB.png)

原本是從表單上讀取欄位來跟數字比較：

```javascript
var today = new Date();
if (
  form.month.value == today.getMonth() + 1 &&
  form.day.value == today.getDate()
) {
  // happy birthday!
}
```

建議改成這樣：

- `+form.month.value`：明確地將表單欄位值強制轉型成 number
- `===`：用嚴格相等性比較，避免強制轉型 (雖然當 `==` 兩邊的值是同型別，行為會與 `===` 一樣)

```javascript
var today = new Date();
if (
  +form.month.value === today.getMonth() + 1 && // strict
  +form.day.value === today.getDate()
) {
  // happy birthday!
}
```

建議：用 `===` 比 `==` 的原因：告知讀 code 的人，此比較沒有發生強制轉型

## 日期比較

直接將日期字串和 `Date` 物件比較會出錯：

```javascript
var date = new Date("1999/12/31");
date == "1999/12/31"; // false

date; // "Fri Dec 31 1999 00:00:00 GMT+0800 (台北標準時間)"
date.toString(); // "Fri Dec 31 1999 00:00:00 GMT+0800 (台北標準時間)"
```

所以應自己寫邏輯來比較：

```javascript
function toYMD(date) {
  var y = date.getYear() + 1900, // year is 1900-indexed
    m = date.getMonth() + 1, // month is 0-indexed
    d = date.getDate();
  return y + "/" + (m < 10 ? "0" + m : m) + "/" + (d < 10 ? "0" + d : d);
}
toYMD(date) === "1999/12/31"; // true
```

### == 運算符的強制轉換規則

| 參數型別 1                          | 參數型別 2                          | 強制轉換規則                                                                                   |
| ----------------------------------- | ----------------------------------- | ---------------------------------------------------------------------------------------------- |
| null                                | undefined                           | 不轉換，總是回傳 true                                                                          |
| null 或 undefined                   | 其他任何非 null 或 undefined 的型別 | 不轉換，總是回傳 false                                                                         |
| 原始型別：string、number 或 boolean | Date 物件                           | 將原始型別轉換成數字;將 Date 物件轉換成原始型別(優先嘗試 toString 方法，再嘗試 valueOf 方法)   |
| 原始型別：string、number 或 boolean | 非 Date 物件                        | 將原始型別轉換成數字;將非 Date 物件轉換成原始型別(優先嘗試 valueOf 方法，再嘗試 toString 方法) |
| 原始型別：string、number 或 boolean | 原始型別：string、number 或 boolean | 將原始型別轉換為數字                                                                           |

[11.9.3 The Abstract Equality Comparison Algorithm](https://www.ecma-international.org/ecma-262/5.1/#sec-11.9.3)

The comparison x == y, where x and y are values, produces true or false. Such a comparison is performed as follows:

==2. If x is null and y is undefined, return true.==
==3. If x is undefined and y is null, return true.==

```javascript
null == undefined; // true
// 寬鬆相等下：引數 null 和 undefined 型別不同，沒有透過強制轉型規則，永為 true。
// 引數型別1 null 或 undefined，與引數型別2 null 或 undefined 以外的進行寬鬆相等，沒有透過強制轉型規則，永為 false。
```

![comparison](https://i.imgur.com/Dt8Q7TQ.png)

undefined 變數已被宣告但尚未賦值。
null 為空值，可以將其賦值給變數。

object 採用 valueOf 或 toString 方法轉為基本型別值。
Date 相反順序嘗試呼叫這兩種方法。
