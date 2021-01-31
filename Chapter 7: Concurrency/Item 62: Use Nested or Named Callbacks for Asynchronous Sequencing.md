# 條款 62 保留非同步執行順序，可使用 bind 消除巢狀或具名的回呼函式

**戡誤**

    Item 62 (中文版已戡誤)
    A couple broken function references (reported by Alexandre Abreu):

    downloadABC calls downloadFiles23 but it should call downloadBC
    downloadBC calls downloadFile3 but it should call downloadC

### 確保非同步的順序 (非同步定序)

為了確保每一步 callback 都可以依序的非同步執行。程式會寫成 callback function 的樣子

```javascript
downloadAsync("file.txt", function(file) {
  console.log("finished");
});
console.log("starting");
```

把呼叫 downloadAsync 函數的程式碼，解讀成「啟動」而非「執行」，啟動完不等執行結束，就印出 starting ，等待執行完成，再印出 finished。確保了 finished 一定在執行完 downloadAsync 之後執行。

### 想在非同步完成之後，再執行非同步呢？

```javascript
db.lookupAsync("url", function(url) {
  // ?
});

downloadAsync(url, function(text) {
  // error: url is not defined
  console.log("contents of " + url + ": " + text);
});
```

最簡單的做法，就是寫成巢狀式 callback。

```javascript
db.lookupAsync("url", function(url) {
  downloadAsync(url, function(text) {
    console.log("contents of " + url + ": " + text);
  });
});
```

為了確保每一步 callback 都可以依序的非同步執行。程式會寫成巢狀 callback function 的樣子
但是如果數量一多，就變得可怕

> 多層巢狀 callback function = 俗稱 callback hell

### 用具名函式將巢狀 callback 拆開


```javascript
db.lookupAsync("url", downloadURL);

function downloadURL (url) {
  downloadAsync(url, function (text) { // still nested
    showContents(url, text);
  });
}

function showContents (url, text) {
  console.log("contents of " + url + ": " + text);
}
```

### 使用 bind 實現柯里化，拆掉閉包

```javascript
db.lookupAsync("url", downloadURL);

function downloadURL (url) {
  downloadAsync(url, showContents.bind(null, url)); // 先給 url, 待 callback 呼叫時就會執行
}

function showContents (url, text) {
  console.log("contents of " + url + ": " + text);
}
```

## 步驟很多的例子

```javascript
db.lookupAsync("url", function (url) {
  downloadAsync(url, function (file) {
    downloadAsync("a.txt", function (a) {
      downloadAsync("b.txt", function (b) {
        downloadAsync("c.txt", function (c) {
          // ...
        });
      });
    });
  });
});
```

用相同的方法拆開，但是會造成奇怪的命名

```javascript
db.lookupAsync("url", downloadURLAndFiles);

function downloadURLAndFiles (url) {
  downloadAsync(url, downloadABC.bind(null, url));
}

// awkward name
function downloadABC (url, file) {
  // duplicated bindings
  downloadAsync("a.txt", downloadBC.bind(null, url, file));
}

// awkward name
function downloadBC (url, file, a) {
  downloadAsync("b.txt", downloadC.bind(null, url, file, a));
}
// awkward name
// more duplicated bindings

function downloadC (url, file, a, b) {
  downloadAsync("c.txt", finish.bind(null, url, file, a, b));
}

// still more duplicated bindings
function finish (url, file, a, b, c) {
     // ...
}
```

折衷方案

```javascript
db.lookupAsync("url", function (url) {
  downloadAsync(url, downloadFiles.bind(null, url));
});

function downloadFiles (url, file) {
  downloadAsync("a.txt", function (url, file, a) {
    downloadAsync("b.txt", function (url, file, a, b) {
      downloadAsync("c.txt", function (url, file, a, b, c) {
        // ...
      });
    });
  });
}
```

條款 66 會有更進一步的整理。

## 現在的做法

有 Promise 囉!!
太多非同步，可以選擇使用 Promise.all, Promise.race, Promise.any