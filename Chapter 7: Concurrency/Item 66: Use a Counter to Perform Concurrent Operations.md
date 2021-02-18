# 條款 66 使用一個計數器來進行共時作業

終於要討論 `downloadAllAsync` 的實作方式。

`downloadAllAsync` 可以一次下載多個檔案，但是無法確定哪個檔案會先下載好，所以如果我們希望維持下載完成的檔案順序，就會有問題 (因為非同步執行，無法確定哪個檔案會先下載好)

```javascript
function downloadAllAsync (urls, onsuccess, onerror) {
  var result = [], length = urls.length;
  if (length === 0) {
    setTimeout(onsuccess.bind(null, result), 0);
    return;
  }
  urls.forEach(function (url) {
    downloadAsync(url, function (text) {
      if (result) {
        // race condition
        result.push(text);
        if (result.length === urls.length) {
          onsuccess(result);
        }
      }
    }, function (error) {
      if (result) {
        result = null;
        onerror(error);
      }
    });
  });
}
```

```javascript
var filenames = [
  "huge.txt", // huge file
  "tiny.txt", // tiny file
  "medium.txt" // medium-sized file
];
downloadAllAsync(filenames, function (files) {
  // 這裡的順序，不保證會如下，會出錯!!
  console.log("Huge file: " + files[0].length);
  console.log("Tiny file: " + files[1].length);
  console.log("Medium file: " + files[2].length);
}, function (error) {
  console.log("Error: " + error);
});
```

為了處理這個狀況，就使用一個變數 pending (待完成) 來確保所有的下載都完成。

並把下載好的檔案放進相對應的 Array 的位置。

> 如果直接用 result.length 來判斷完成數量，會在最後一個 index 先完成時，造成誤判

```javascript
function downloadAllAsync (urls, onsuccess, onerror) {
  var pending = urls.length;  // 先設定全部有幾個待完成
  var result = [];
  if (pending === 0) {
    setTimeout(onsuccess.bind(null, result), 0); return;
  }
  urls.forEach(function (url, i) {
    downloadAsync(url, function (text) {
      if (result) {
        result[i] = text; // store at fixed index
        pending--; // register the success
        if (pending === 0) {
          onsuccess(result);
        }
      }
    }, function (error) {
      if (result) {
        result = null;
        onerror(error);
      }
    });
  });
}
```