# 條款 56 避免非必要的狀態

一個無狀態的 API 所提供的韓式或方法之行為，只取決於他們的輸入，而不仰賴程式的狀態變化。無狀態的函式類似於 `toUpperCase`：

```javascript
'foo'.toUpperCase();
```

上方的程式碼不論在何時被執行，都會得到 `Foo`。另一方面 `Date` 就是有狀態的，被建構出來的 `Date` 物件狀態會一直改變，因此每次執行 `Date` 所提供的 API 時得到的結果都會不同。

有狀態的 API 還有 Canvas，每一行的設置都會影響到接下來的執行，例如下方的 `fillText` 會因為上方對 `c` 的設置而改變最後執行的結果：

```javascript
c.fillStyle = "blue";
c.font = "24pt serif";
c.textAlign = "center";

c.fillText("hello, world!", 75, 25);
```

另一個例子是像這樣子，不曉得 `drawMyImage` 有沒有改變了 `c` 的狀態導致 `fillText` 的行為不如預期：

```javascript
c.fillStyle = "blue";
drawMyImage(c); // did drawMyImage change c?
c.fillText("hello, world!", 75, 25);
```

盡可能的選用無狀態的 API，如果非得選用有狀態的，請在說明文件中寫下每一個行為和狀態的依賴關係。
