# 條款 33 如果你的建構式不用 new 呼叫的話

```javascript
function User() {
  const isConstructor = this instanceOf User
  if (!isConstructor) throw ConstructorError() ;
  // 其它的建構式內容

}
```

## Class 是有用的

```javascript
class User {
    constructor(){}
}

User(); // TypeError
```


