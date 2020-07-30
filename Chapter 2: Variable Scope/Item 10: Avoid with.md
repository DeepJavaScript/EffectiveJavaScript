# 條款 10 避免使用 with

JavaScript中，可能沒有其它單一功能比 `with` 還要有害了。
`with` 的亞名昭彰可說是罪有應得，不管它提供了什麼便利性，遠不足彌補它所帶來的不可靠與低落效率。

有時候需要在單一物件上呼叫方法，`with` 就是很方便的東西，例如：

```javascript
const player = {
  name: 'Clark',
  age: 26,
};

with(player) {
  console.log(`Hello, my name is ${name}, and I am ${age} years old.`);
};
```

但是如果使用 `with` 的話，你就無法分辨哪些變數或方法不是該物件的。

```javascript
const f = (x, y) => {
  with(Math) {
    Math.x = 0;
    return min(round(x), sqrt(y));
  };
};
```

在上方的例子中，對 `Math` 使用 `with`，所以可以直接使用 `Math` 的 `min`、`round` 或 `sqrt` 等方法，但你就不會知道變數 `x` 和 `y` 是不是 `Math` 的，就算你知道他是外面傳進來的參數，但如果在 `Math` 內也有 `x` 與 `y` 的話，那就會使用 `Math` 的，而不是傳進函式 `f` 的參數。

因為 `with` 會把整個 Object 當作一個新的作用域（scope），然後慢慢尋找到他的 prototype，確認完全沒有屬性後，才會跳出 `with` 往外找。

> 注意（但其實也沒在用 `with` 沒看也沒差）
> 1. `with` 綁定的作用域裡面有哪些東西，是以**執行當下**的 object 為主。
>
>  ```javascript
>  const f = (x, y) => {
>    with(Math) {
>      console.log({x, y})
>      Math.x = 0;
>      return min(round(x), sqrt(y));
>    };
>  };
>  f(1, 2)
>  // {x: 1, y: 2} // console 的當下 Math 沒有 x 和 y
>
>  f(1, 2)
>  // {x: 0, y: 2} // console 的當下 Math 有 x 了，x 的值是 0。
>
>  Math.y = 123
>  
>  f(1, 2) // console 的當下 Math 有 x 也有 y 了，x 的值是 0，y 是 123。
>  // {x: 0, y: 123}
>  ````
>
> 2. 也因為 `with` 自己創造了一個作用域，JS 執行的時候就必須要在 `with` 內找過一輪，確認沒有該變數或方法後才會跳出，所以執行速度會比較慢。

也就是說，在使用 `with` 的時候，你與你團隊的夥伴都有十足的信心知道該 Object 中擁有哪些屬性，或有哪些方法一定沒有。誰能有把握這麼說呢？更重要的是這麼做也限制了你對 Object 的未來性，如同上面的例子，在對 `Math` 使用了 `with` 的當下之後，你便無法再添加變數 `x` 或 `y` 到 `Math` 裡（當然平常不會對 `Math` 動手動腳啦）。

目前沒有能夠代替 `with` 的功能，替代方案就是把變數名稱取短一點，**但還是別用縮寫會比較好**，例如：

```javascript
const fr = new FileReader();
```

總之就是，拿掉了 `with`，所有變數的來源都清清楚楚。