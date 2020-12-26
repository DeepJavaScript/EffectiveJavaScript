# 條款 48 避免在列舉時修改一個物件

## 一邊跑 for-in ，同時修改物件

### 正常的情況

```javascript
var a = { name: "chris", age: 18 }
undefined
for (key in a) {
    console.log(a[key])
}
```

```shell
chris
18
```

### 刪除屬性，會變 undefined

```javascript
var a = { name: "chris", age: 18 }
for (key in a) {
    delete a['name'];
    console.log(a[key])
}
```

```shell
undefined  ## 原本應該是 chris
18
```

### 增加屬性，不會改變什麼 (還是要避免比較好)

```javascript
var a = { name: "chris", age: 18 }
for (key in a) {
    a['newProperty'] = "newValue";
    console.log(a[key])
}
```

```shell
chris
18
```

## 書裡的例子

```javascript
function Member(name) {
    this.name = name;
    this.friends = [];
}

Member.prototype.inNewrork = function (other) {
    var visited = {};
    var workset = {};

    workset[this.name] = this;

    for (let name in workset) {
        let member = workset[name];
        delete workset[name]; // 造成問題

        if (name in visited) {
            continue;
        }

        visited[name] = member;
        if (member === other) {
            return true;
        }
        member.friends.forEach(function(firend) {
            workset[firend.name] = firend;
        })
    }
    return false;
}
```

```
       C
      / \
A -> B   E -> F
      \ /
       D
```

```javascript
var a = new Member('Alice')
var b = new Member('Bob')
var c = new Member('Carol')
var d = new Member('Dieter')
var e = new Member('Eli')
var f = new Member('Fatima')

a.friends.push(b);
b.friends.push(c);
c.friends.push(e);
d.friends.push(b);
e.friends.push(d, f);

a.inNewrork(f) //false
```

進行 `for-in` 的同時，修改了 Array 並不保證修改結果會在當時的 for-in 中出現
