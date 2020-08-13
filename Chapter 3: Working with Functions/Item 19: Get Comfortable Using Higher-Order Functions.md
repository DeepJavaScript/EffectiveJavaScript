# 條款 19 熟悉高階函式的用法

heigher-order functions 過去 functional programming 僧侶們的特有用語 (shibboleth)

看起來像是一種高階程式技巧的深奧術語，但事實並非如此

只是會做到這兩件事而已

1. 把函式當作引數。
2. 在自身呼叫參數函數。 
3. 回傳函式作為回傳值。

整理 heigher-order function 的心法

- 函式內容有些重複，可以將不重複的部份抽離成 callback

常用的地方: 

- Array methods 都算是 heigher-order function

