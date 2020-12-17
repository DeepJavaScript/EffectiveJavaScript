## 條款 35: 使用 Closures 來儲存私有資料

**戡誤**

        Page 95, 2nd paragraph: Change "variables of the constructor" to "parameters of the constructor".
## ES6 語法: Class 的私有

```javascript
class User {
    #name
    constructor(name){
        this.#name = name
    }
    #getName() {
        return this.#name
    }
    getPrivateName() {
        return "private" + this.#getName()
    }
}
```