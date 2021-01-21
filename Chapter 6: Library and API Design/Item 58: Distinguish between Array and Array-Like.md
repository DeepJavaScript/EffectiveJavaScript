# 條款 58 區別陣列及類陣列物件

如果要區別 Array 和 Object 的話，不論是用 `length`（Object 也可以有名字是 `length` 的特性）或是 `instanceof`（Object 繼承了 Array）來判斷可能都會有問題。

所以在 ES5 以上可以使用 `Array.isArray` 來確定某個變數真的是 Array 而不是 Object，在 ES5 以下的話則用 `Object.prototype.toString` 來判斷。
