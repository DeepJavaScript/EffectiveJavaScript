# 條款 61 別阻斷 I/O 上的事件佇列

JavaScript 被設計成 embedded scripting language ，一開始並不會成為單獨運作的應用程式。

撰寫能夠回應多個 concurrnet events 的 JavaScript 程式，這種簡單的執行模型，有下列幾種同義詞。

1. 事件共時性 (event-queue concurrency)
2. 者事件迴圈共時性 (event-loop concurrency)
3. 非同步 (asynchronous)

ECMAScript 標準並不稱之為 concurrency

## 正文開始

||sync/async|blocking/non-blocking|
|-|-|-|
|不等待|非同步: 條件成立再執行|non-blocking: 不等待|
|等待|同步: 輪到我就執行|blocking: 等待|

非同步執行，並不是非同步執行結束就馬上執行 callback，而是提供「執行完成 (run to completion) 的保證。
優點: 比起 thread 和 lock 來得簡單很多。
缺點: 在執行某一個程式時，會 blocking 其它的程式。

在一個不受主程式影響的環境中，blocking 就不會有這麼多的問題。例如 web 上的 worker 就可以擁有真正的 concurrency 並且獨立於主程式的執行環境。
它無法取全域 scope，在一個 worker 中，同步版本的 XMLHttpRequest 較不容易發生問題。

Thread 的特色，是與主程式共享記憶體，而 worker 是獨立於主程式的環境，不共享記憶體，就不會干擾程式碼執行。

**example: 下載檔案**

同步

blocking 程式適合在 worker 裡執行，阻斷自己的 worker 而已。

```javascript
var text = downloadSync("http://example.com/file.txt");
console.log(text);
```

非同步

非同步，適合處理事件

```javascript
downloadAsync("http://example.com/file.txt", function(text) {
  console.log(text);
});
```

## 多執行緒的安排

- 畫面執行緒 = 主執行緒，若執行 blocking 會造成 UX 不好
- 繁重的運算，應該在背景執行緒執行，可以開 work 執行同步 API。
