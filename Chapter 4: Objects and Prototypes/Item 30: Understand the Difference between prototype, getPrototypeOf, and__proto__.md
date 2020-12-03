# 條款 30 了解 prototype、getPrototypeOf 與 __proto__ 之間的差異

```
Function.prototype
+------------+
| .apply     |
+------------+
| .bind      |
+------------+
| .call      |
+------------+
      ^
      |
User  | (Function)
+------------+ <--+     User.prototype
| .prototype |-----\--> +-----------------+
+------------+      +---| .constructor    |
                        +-----------------+
                        | .toString       |
                        +-----------------+
                        | .checkPassword  |
                        +-----------------+
              getPrototypeOf(u) ^ |
        (lookup prototype of u) | |
                                | |
            (繼承關係) __proto__ | |
                                | |
                                | |
                                | | Object.create(User.prototype)
                        u       | v
                        +-----------------+
                        | .name           |
                        +-----------------+
                        | .passwordHash   |
                        +-----------------+
```

1. Function 有 `.prototype`  屬性
   - `prototype` 是一個「擁有 constructor 的」 Object。
2. Object 有兩種方法吐出「有建構式的 `.prototype` 」物件。
   - `.getPrototypeOf()` (ES5 標準)
   - `__proto__` (非標準)

> **ECMA-262 已將 `__proto__` 納為標準**
>
> B.2.2.1 `Object.prototype.__proto__`
>
> B.3.1 `__proto__` Property Names in Object Initializers