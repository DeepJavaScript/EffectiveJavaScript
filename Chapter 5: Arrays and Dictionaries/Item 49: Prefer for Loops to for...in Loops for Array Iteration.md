# 條款 49 陣列迭代時優先選用 `for` 迴圈，而非 `for...in` 迴圈

## `for-in` 時的 `key` 是 `index` 但是，它是字串

`for-in` 原本是設計給 Object 進行迭代，而且 Object 的 key 值永遠是 字串
所以在下面這段程式碼的 `key` 值

```javascript
var array = [1, 2, 3, 4, 5]
for (key in array) {
    console.log(typeof key, key)
}
```

```shell
string 0
string 1
string 2
string 3
string 4
```

## 新標準的 `for-of`

`for-of` 設計來給 Array，因為 Array 的 key 值永遠都是 0 起始計數的 index 值，所以 for-of 直接給 value 值。

```javascript
var array = [1, 2, 3, 4, 5]
for (value of array) {
    console.log(typeof value, value)
}
```

```shell
number 1 ## 這是 value 值，不是 index
number 2
number 3
number 4
number 5
```

## 使用 for loop

使用 for loop，也可以使用 Array method
