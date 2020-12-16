# 條款 37 認出 this 的隱含繫結

**戡誤**

    Item 37
    According to the page "This seemingly simple code has a major but subtle bug: The callback passed to lines.map refers to this, expecting to extract the regexp property of the CSVReader object. But map binds its callback's receiver to the lines array, which has no such property." However, if the second parameter of map is not in use, the global object (in this case window) associated with callback will be used instead, and not the lines array. (reported by Tan Gek Hua)

## function 當作 callback 時

```javascript
const User = {
  name: "chris",
  skills: ["html", "css", "javascript"],
  fullName: function () {
    return this.field.map(function () {

    })
  }
}
```

this 是一種 runtime 的產物。

![](https://i.imgur.com/wHNKgfN.png)

書中提到的例子是 arrow function 出現之前 function 當 callback 發生的問題

