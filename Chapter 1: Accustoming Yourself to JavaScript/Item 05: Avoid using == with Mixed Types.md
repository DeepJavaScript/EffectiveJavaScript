# 條款 05 避免把==用在混合的型別上

`==` 是轉型比對
`===` 是不轉型比對

### 條款 05 避免把 `==` 用在混合的型別上
相等比較符：
- `==` 會強制轉型兩個引數，再做比較，應避免使用，除非開發者能夠熟背所有強制轉型的規則。
- `===`（不會強制轉型地比較兩個值） 是比較嚴謹的。
> [Testing and Comparison Operations | read262](https://read262.netlify.app/abstract-operations/testing-and-comparison-operations#sec-abstract-equality-comparison)