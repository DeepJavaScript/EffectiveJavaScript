# 條款 63 小心遺漏的錯誤

非同步的錯誤處理，難！
同步的錯誤處理，簡單！

```javascript
try {
  f();
  g();
  h();
} catch (e) {
// handle any error that occurred...
}
```

但是面對不同的非同步程式碼執行時，要了解它是被折成佇列上的不同程式碼執行。
所以不可能包在同一個 try 區塊中。

## 非同步 API 的錯誤處理 callback

事實上，非同步 API 甚至無法擲出例外。因為我們只是啟動它，而不是執行它，發生錯誤時，還是只能使用 callback 並且傳入特殊引數代表錯誤。

以條款 61 的例子，在檔案下載時發生錯誤，API 會呼叫位於第 3 個引數的 callback

```javascript
downloadAsync("http://example.com/file.txt", function (text) {
  console.log("File contents: " + text);
}, function (error) {
  console.log("Error: " + error);
});
```

多重檔案下載，就會出現 callback hell

```javascript
downloadAsync("a.txt", function (a) {
  downloadAsync("b.txt", function (b) {
    downloadAsync("c.txt", function (c) {
      console.log("Contents: " + a + b + c);
    }, function (error) {
      console.log("Error: " + error);
    });
  }, function (error) {
    console.log("Error: " + error); // repeated error-handling logic
  });
}, function (error) {
  console.log("Error: " + error); // repeated error-handling logic
});
```

重複的錯誤處理

```javascript
function onError (error) {
  console.log("Error: " + error);
}

downloadAsync("a.txt", function (a) {
  downloadAsync("b.txt", function (b) {
    downloadAsync("c.txt", function (c) {
      console.log("Contents: " + a + b + c);
    }, onError);
  }, onError);
}, onError);
```

## node style 的錯誤處理風格

```javascript
function onError (error) {
  console.log("Error: " + error);
}

downloadAsync("a.txt", function (error, a) {
  if (error) {
    onError(error);
    return;
  }
  downloadAsync("b.txt", function (error, b) { // duplicated error-checking logic
    if (error) {
      onError(error);
      return;
    }
    downloadAsync(url3, function (error, c) { // duplicated error-checking logic
      if (error) {
        onError(error);
        return;
      }
      console.log("Contents: " + a + b + c);
    });
  });
});
```

通常就會捨棄「if statements to span multiple lines with braced bodies」

```javascript
function onError (error) {
  console.log("Error: " + error);
}

downloadAsync("a.txt", function (error, a) {
  if (error) return onError(error);
  downloadAsync("b.txt", function (error, b) { // duplicated error-checking logic
    if (error) return onError(error);
    downloadAsync(url3, function (error, c) { // duplicated error-checking logic
      if (error) return onError(error);
      console.log("Contents: " + a + b + c);
    });
  });
});
```

try-catch 與非同步 API 中典型的錯誤處理邏輯之間的一個實務差異在於，try 讓定義 catch all 的邏輯變簡單。
而非同步 API時非常容易忘記進行錯誤處理，通常都會漏掉。

不良的錯誤處理或漏接，都是程式可靠度的損失，也是使用者挫折感的來源之一，因為程式在出錯時沒有提供任何的回饋資訊。
同樣的那種粉飾太平的錯誤處理方式也是除錯的大忌。

最好的解藥就是事先預防，使用非同步 API 時要警覺，是不是明確的處理了所有的錯誤狀況。

> 我把 `downloadAllAsync` 的程式都拿掉，因為一點幫助也沒有。

## 現在的做法

有 Promise 囉!!
太多非同步，可以選擇使用 Promise.all, Promise.race, Promise.any

