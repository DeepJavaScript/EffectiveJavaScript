# 條款 33 如果你的建構式不用 new 呼叫的話


## 限制誤用

阻止別人的誤用，可以在 constructor 裡先加上這一行。讓不用 new 呼叫時會報錯。

```javascript
function User() {
  const isConstructor = this instanceOf User
  if (!isConstructor) throw ConstructorError() ;
  // 其它的建構式內容

}
```

## 使用 Class ，也會限制誤用

不用 new 呼叫 class 定義的 constructor 會拋出 TypeError

```javascript
class User {
    constructor(){}
}

User(); // TypeError
```


