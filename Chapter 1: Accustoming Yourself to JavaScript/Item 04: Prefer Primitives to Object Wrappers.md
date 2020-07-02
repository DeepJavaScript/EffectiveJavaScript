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