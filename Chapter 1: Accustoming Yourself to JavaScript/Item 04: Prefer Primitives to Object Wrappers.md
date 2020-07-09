# 條款 04 優先選用基本型別而非物件包覆器

## 透過「字面值」建立與透過「建構器」建立的比較

JavaScript 可以透過建構式或字面值來宣告一個基本值，兩者很類似：

> 基本值：
> 除了物件外，JavaScript 有 5 個原始型別：boolean、number、string、null 和 undefined，同時，JavaScript 提供了建構函式來封裝 boolean、number 和 string 成為物件，在此用 string 來示範。

```javascript
// 透過建構式
var name1 = new String("Leon");

// 透過字面值
var name2 = "Leon";

// 都有相同的特性
name1[0] = "L";
name2[0] = "L";
```

但是這兩者是有差異的（型別不同）：

- `String()` 是物件
- 字串 literal 是字串

```javascript
name1 == name2; // true
name1 === name2; // false

typeof name1; // object
typeof name2; // string
```

## 隱含地建立物件 wrapper

基本值建構器實際上沒有什麼用處，除了它的 methods：

```javascript
name1.toUpperCase(); //'LEON'
```

1. 基本型別的 wrapper，是放置基本型別需要的 `method` ，而不是用 `constructor` 建立物件。

2. 可是如果用字面值建立的，就不是物件，也就沒有相應的方法可以取用。當你要用那些 utility methods 時，JS 會自動幫你用物件 wrapper 將基本型別值包起來，然後就能夠擷取出基本型別值的 property 來呼叫這些方法。

3. 如此一來，透過字面值產生的字串值變數也可以調用方法，它會在調用的當下自動包覆 wrapper，讓它得以取得該方法。

```javascript
name2.toUpperCase(); //'LEON'
```

4. 由於是當下包覆，執行完該方法後，就又解除包覆，所以在基本值上設定屬性是沒有用的：

```javascript
name2.setProperty = "42";
name2.setProperty(); //TypeError
```

> 整理：
>
> - 進行相等性比較時，物件 wrapper 和基本型別值的行為不同
> - 取用或設定基本型別上的 property 時，會隱含地建立物件 wrapper，而且是當下包裹，完成後當下解除。

[The history of “typeof null”](https://2ality.com/2013/10/typeof-null.html)

The “typeof null” bug is a remnant from the first version of JavaScript. In this version, values were stored in 32 bit units, which consisted of a small type tag (1–3 bits) and the actual data of the value. The type tags were stored in the lower bits of the units. There were five of them:

- 000: object. The data is a reference to an object.
- 1: int. The data is a 31 bit signed integer.
- 010: double. The data is a reference to a double floating point number.
- 100: string. The data is a reference to a string.
- 110: boolean. The data is a boolean.
