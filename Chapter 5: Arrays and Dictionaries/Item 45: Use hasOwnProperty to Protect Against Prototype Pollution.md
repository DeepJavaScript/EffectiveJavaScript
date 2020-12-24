# 條款 45 使用 hasOwnProperty 來防止原型汙染

**戡誤**

    Item 45
    Another viable implementation approach is to add a prefix string like "%", "$", or "~" to every dict entry's internal property name, to ensure that no entry can possibly be stored with the name "__proto__".
    加上前綴確保不會蓋掉 __proto__

找到物件的 property 有幾種行為

1. enumeration (查物件)
2. lookup (查原型)

```javascript
var human = { name: 'lucy', age: 3200000 }
var chris = Object.create(human)
chris.skills = ['javascript']

'name' in chris  //true
chris.hasOwnProperty('name') //false
'skills' in chris  //true
chris.hasOwnProperty('skills') //true
```

