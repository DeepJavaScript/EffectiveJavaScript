# 條款 42 避免輕率的 Monkey-Patching

Monkey-Patching（猴子修補法），意思是無止盡的因為自己的需求修改 Prototype 的方法。因為 Prototype 上的方法是所有 Instance 共用的，所以修改的話可能會導致其他 Instance 在執行 Prototype 的行為出現問題。

另外正面的 Monkey-Patching（猴子修補法）用法在於 Polyfills，Polyfills 先判斷 window 物件是否有某個方法存在，如果該方法不存在（瀏覽器不支援），那就直接在 window 物件上加上一些未來的語法，若是該方法存在則不做任何事情。
