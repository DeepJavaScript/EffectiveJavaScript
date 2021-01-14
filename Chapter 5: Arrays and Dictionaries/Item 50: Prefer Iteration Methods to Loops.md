# 條款 50 優先使用迭代方法而非迴圈

避免開發者花時間閱讀，控制迴圈的細節，增進看 code 的速度與迴圈控制不小心出錯的問題。

所以，優先使用 Array method 的 forEach 勝過 for loop。

> 如果不確定執行次數的話，請用 `while`。

P146. 提到可以把 `takeWhile` 定義到 `Array.prototype` 中，但這是錯的，請不要隨便異動 `prototype`。
