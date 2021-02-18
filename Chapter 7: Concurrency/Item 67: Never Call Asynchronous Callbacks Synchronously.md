# 條款 67 永遠不要同步地呼叫非同步的回呼函式

> setTimeout 是用來設定非同步的超時回覆。

```javascript
var cache = new Dict();

function downloadCachingAsync (url, onsuccess, onerror) {
  if (cache.has(url)) {
    onsuccess(cache.get(url)); // synchronous call
    return;
  }
  return downloadAsync(url, function (file) {
    cache.set(url, file);
    onsuccess(file);
  }, onerror);
}
```

為了避免非同步 API 連續呼叫時，出現問題


```javascript
downloadAsync("file.txt", function (file) {
  console.log("finished");
});
console.log("starting"); // 先執行 1

downloadAsync("file.txt", function (file) {
  console.log("finished"); // 先執行 2 出問題
});
console.log("starting");
```

所以，要將 `if (cache.has(url))` 成立時執行的內容改成非同步，就可以避免上述的問題

```javascript
var cache = new Dict();

function downloadCachingAsync (url, onsuccess, onerror) {
  if (cache.has(url)) {
    onsuccess(cache.get(url)); // synchronous call
    return;
  }
  return downloadAsync(url, function (file) {
    cache.set(url, file);
    onsuccess(file);
  }, onerror);
}
```


