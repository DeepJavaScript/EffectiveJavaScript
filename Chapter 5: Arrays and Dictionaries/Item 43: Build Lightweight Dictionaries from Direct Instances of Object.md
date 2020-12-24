# 條款 43 從 Object 的直接實體建置出輕量化的字典

物件當作容器

- name-value associations
- dense, sparse matrix
- hash table


1. 條款 47: 無論何時，任何人都不該在 Object.prototype 加入特性，污染 `for-in`
2. 條款 42: 新增特性到 Array 也不合理。會有 Polyfills
3. 堅持使用 Object 的直接實體當作輕量化字典，能夠避免 `for-in` 迴圈受到污染。

for Perl PHP developer 要小心
