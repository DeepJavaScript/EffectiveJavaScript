# 條款 38 從子類別的建構式呼叫超類別的建構式

物件導向理論本來就會這樣做。

```
scene graph
+------------+ <--+     actor
| content    |    |     +------------+
| width      |    +-----| sence      |
| height     |          | x          |
| images     |          | y          |
| []         |          +------------+
+------------+          | moveTo     |
| register   |          +------------+
+------------+          | exit       |
| unregister |          +------------+
+------------+          | draw       |
| draw       |          +------------+
+------------+          | width      |
                        +------------+
                        | height     |
                        +------------+
                             Δ
                             |
                        SpaceShip
                        +------------+
                        |            |
                        +------------+
```

SpaceShip 的建構式要初始 Actor

```javascript
function Actor (...parameters) {
  // ...
  this.type = "Actor";
}
```

```javascript
function SpaceShip (...parameters) {
  Actor.call(this, ...parameters)
  this.points = 0;
}
```

繼承

```javascript
SpaceShip.prototype = Object.create(Actor.prototype);
```

當初始化發生時，會執行 SpaceShip 建構式，開始時會先初始 Actor 的建構式。


