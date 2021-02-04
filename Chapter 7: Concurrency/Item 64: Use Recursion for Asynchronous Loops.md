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

想要實作的，是行為類似迴圈，但我們明確的指示前，不會繼續執行程式碼。(現在就是用 generator function)，可以透過迭代進行。

```javascript
function downloadOneAsync (urls, onsuccess, onfailure) {
  var n = urls.length;
  function tryNextURL (i) {
    if (i >= n) {
      onfailure("all downloads failed");
      return;
    }
    downloadAsync(urls[i], onsuccess, function () {
      tryNextURL(i + 1);
    });
  }
  tryNextURL(0);
}
```

tryNextURL 是遞迴

## 錯誤處理

```javascript
function countdown (n) {
  if (n === 0) {
    return "done";
  } else {
    return countdown(n - 1);
  }
}
countdown(100000); // error: maximum call stack size exceeded
```

同步的呼叫次數太多，會造成太長的 call stack

若使用非同步的遞迴，只會有一個 call stack 消除之前的 function call，下一次的 function call 會放入 event loop。

不會造成太長的 call stack。

