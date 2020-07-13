# 條款 09 總是宣告區域變數
> 如果要說有什麼事情會比一個全域變數還要麻煩的，那就是非刻意的全域變數 (中文書p.36)

## JS 的規則困擾
依照 JS 的規則，如果忘記宣吿一個區域變數，它就會自動變成一個全域變數，且不會有錯誤發生，造成預料外的環境污染。

錯誤範例：
```javascript
function swap(a,i,j){
  temp = a[i]; // temp 自動變成全域變數
  a[i] = a[j];
  a[j] = temp;
}
```

正確範例：
```javascript
function swap(a,i,j){
  let temp = a[i];
  a[i] = a[j];
  a[j] = temp;
}
```
- 應使用 `let` 或 `const` 明確宣告區域變數

---

## 解決方式
使用工具 ESLint 協助檢查。
ESLint 有一規則是 `no-undef` (disallow the use of undeclared variables unless mentioned in `/*global */` comments)。
- 更多規則[點我參考](https://eslint.org/docs/rules/)