# 條款 48 避免在列舉時修改一個物件



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
        delete workset[name];

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