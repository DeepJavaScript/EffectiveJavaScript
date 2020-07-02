# 條款 04 優先選用基本型別而非物件包覆器

基本型別的物件包覆器，是放置基本型別需要的 `method` ，而不是用 `constructor` 建立物件。
### 條款 04 優先選用基本型別而非物件包覆器
JavaScript 可以透過建構式或字面值來宣告一個字串值變數：
```javascript
// 透過建構式
var name1 = new String('Leon');

// 透過字面值
var name2 = 'Leon';
```
但是這兩者是有差異的（型別不同）：
```javascript
name1 == name2;   // true
name1 === name2;  // false

typeof(name1);    // object 
typeof(name2);    // string
```
建構器 String 基本上沒有什麼用處，除了它的 methods：
```javascript
name1.toUpperCase(); //'LEON'
```
而透過字面值產生的字串值變數也可以調用方法，是因為 JavaScript 會在調用的當下自動包覆成 String 物件，讓它得以取得該方法。
```javascript
name2.toUpperCase(); //'LEON'
```
由於是當下包覆，執行完該方法後，就又解除包覆，所以在基本值上設定屬性是沒有用的：
```javascript
name2.setProperty = '42';
name2.setProperty();  //TypeError

整理：
- 進行相等性比較時，物件 wrapper 和基本型別值的行為不同
- 取用或設定基本型別上的 property 時，會隱含地建立物件 wrapper

## 物件 wrapper 相等性比較

用物件 wrapper 建立的值和基本型別值 (primitive value) 還是不同，雖然在使用上很像。

例如：利用 `String()` 字串 wrapper 來建立字串 vs 字串 literal：

```javascript
var s = new String("hello");

s + " world";    // "hello world"
s[4];            // "o"
```

- `String()` 是物件
- 字串 literal 是字串

```javascript
typeof "hello";  // "string"
typeof s;        // "object"
```

所以無法比較 wrapper (物件會比較參考是否相同)：

```javascript
var s1 = new String("hello");
var s2 = new String("hello");
s1 === s2;  // false
s1 == s2;   // false
```

## 隱含地建立物件 wrapper

物件 wrapper 看視沒用，但它的用處在於它的 utility method。

當你要用那些 utility method 時，JS 會很像是用物件 wrapper 將基本型別值包起來，然後就能夠擷取出基本型別值的 property 來呼叫這些方法。

例如：

```javascript
"hello".toUpperCase(); // "HELLO"
```

所以你能在基本型別值上設定 property，但不會有效果：

```javascript
var s = "hello";
s.someProperty = 17;
s.someProperty;  // undefined
```

因為每次隱含的 wrap 動作會產生新的 `String` 物件。

註：在基本型別值上設定 property 超沒意義的...
