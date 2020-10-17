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
User  |
+------------+ <--+     User.prototype
| .prototype |-----\--> +-----------------+
+------------+      +---| .constructor    |
                        +-----------------+
                        | .toString       |
                        +-----------------+
                        | .checkPassword  |
                        +-----------------+
               getPrototypeOf() ^
                   (lookup)     | __proto__
                        u       | (繼承關係)
                        +---------------+
                        | .name         |
                        +---------------+
                        | .passwordHash |
                        +---------------+
```

1. Function 才有 `.prototype` ，它會是一個「擁有 constructor 的」 Object。
2. Object 才有 `.getPrototypeOf()` (ES5 標準) 和 `__proto__` (非標準) 吐出同一個 Object (就是建構式的 `.prototype`)。