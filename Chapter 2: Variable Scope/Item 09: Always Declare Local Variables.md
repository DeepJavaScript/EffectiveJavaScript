# 條款 09 總是宣告區域變數

**戡誤**

    Item 9
    Note that accidental creation of globals is prevented in ES5 strict mode, because assigning to an unbound variable is an error and throws an exception. (Leaving off a var can still lead to nasty bugs if the variable name happens to coincide with a variable that already exists in an outer scope.)

## 摘要

1. 總是使用 var 來宣告新的區域變數
2. 考慮使用 lint 工具來協助你找出未繫結的變數 (unbound variables)

## JS 的規則困擾

> 如果要說有什麼事情會比一個全域變數還要麻煩的，那就是非刻意的全域變數 (中文書p.36)

依照 JS 的規則，如果忘記宣吿一個區域變數，它就會自動變成一個全域變數，且不會有錯誤發生，造成預料外的環境污染。

錯誤範例:
```javascript
function swap(a,i,j){
  temp = a[i]; // temp 自動變成全域變數
  a[i] = a[j];
  a[j] = temp;
}
```

正確範例:
```javascript
function swap(a,i,j){
  let temp = a[i];
  a[i] = a[j];
  a[j] = temp;
}
```

## 優先使用 `const` ，必要時才改用 `let`

> 應使用 `let` 或 `const` 明確宣告區域變數

以[最小權限原則](https://zh.wikipedia.org/wiki/%E6%9C%80%E5%B0%8F%E6%9D%83%E9%99%90%E5%8E%9F%E5%88%99)，可以在宣告變數時，永遠優先使用 `const`，在完全不必要換掉變數的 reference 的情況不會出問題。

若需要改變變數的 reference 時，使用 `let` 宣告變數，允許變數的 reference 在執行時期可以被修改

## 使用 lint 工具的解決方案
使用工具 ESLint 協助檢查。
ESLint 有一規則是 `no-undef` (disallow the use of undeclared variables unless mentioned in `/*global */` comments)。
- 更多規則[點我參考](https://eslint.org/docs/rules/)