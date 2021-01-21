# 條款 60 支援方法鏈串

方法鏈串又被稱作為 fluent style（流暢風格，這個術語是由 Smalltalk 的「method cascades」的程式設計師所創造），方法串鏈就像原生的 Array Method：

```javascript
var users = records.map(function(record) {
        return record.username;
    }).filter(function(username) {
        return !!username;
    }).map(function(username) {
        return username.toLowerCase();
    });
```

這種風格非常具有彈性和表達力，在無狀態的 API 中會回傳相同 Prototype 的 Object（所以才可以以回傳的結果執行同樣 Prototype 的 Method）。而在有狀態的 API 中則是會不斷修改同一個物件以得到最後的結果。
