# 條款 20 使用 call 以自訂的接收者來呼叫方法

一般來說 method 的 this 是呼叫 method 的物件。
但是我們有時需要自定義某個函式(包含 function/method) 的 this，指定成我們想要的物件。
但是該物件的屬性並不擁有這個函式，無法直接使用。

**方法一: (不推薦)**

在該物件中，加上這個方法

```javascript
obj.tempMethod = fn;
obj.tempMethod(arg1, arg2);
delete obj.tempMethod;
```

限制: 物件可能被 frozen 或 sealed，新特性就無法加入了。

**方法二:**

使用函式方法 call

```javascript
f.call(obj, arg1, arg2)
```

類似

```javascript
f(obj, arg1, arg2)
```

第一個參數就是指定 this 的位置。



