# 條款 64 使用遞迴來進行非同步迴圈

一個由 URL 的陣列，想要依序下載它們。一直到有一個成功了為止。

```javascript
function downloadOneSync (urls) {
  for (let i = 0, n = urls.length; i < n; i++) {
    try {
      return downloadSync(urls[i]);
    } catch (e) { }
  }
  throw new Error("all downloads failed");
}
```

但是，用在 async 時，就不適合這樣寫
無法滿足在進行錯誤處理之後，才進行下一個非同步程式。

```javascript
function downloadOneAsync (urls, onsuccess, onerror) {
  for (var i = 0, n = urls.length; i < n; i++) {
    downloadAsync(urls[i], onsuccess, error => {
      // ?
    });
    // loop continues
  }
  throw new Error("all downloads failed");
}
```


