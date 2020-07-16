# 條款 08 盡量少用全域物件
摘要
1. 避免宣告全域變數
2. 盡量將變數宣告在 black scope 
3. 避免為全域物件新增特性
4. 使用全域物件來進行平台的功能偵測

## 1. 避免宣告全域變數 & 2. 盡量將變數宣告在 black scope 
全域變數污染
```javascript
//範例8.1
var players=[
	{
		player: 'A',
		levels:[
			{
				score: 99
			},
			{
				score: 29
			},
			{
				score: 39
			}
		]
	},
	{
		player: 'B',
		levels:[
			{
				score: 10
			},
			{
				score: 20
			},
			{
				score: 30
			}
		]
	}
];

var i, n, sum;
function averageScore(players){
	sum = 0;
	for(i=0, n=players.length; i < n; i++){
		console.log('>>>>>> averageScore',n)
		sum += score(players[i]);
	}
	debugger;
	return sum / n;
};
function score(player){
	sum = 0;
	for(i=0, n=player.levels.length; i < n; i++){
		console.log('>>>>>> score',n)
		sum += player.levels[i].score;
	}
	debugger;
	return sum;
};

averageScore(players);

```

改寫
```javascript
//範例8.2
var players=[
	{
		player: 'A',
		levels:[
			{
				score: 99
			},
			{
				score: 29
			},
			{
				score: 39
			}
		]
	},
	{
		player: 'B',
		levels:[
			{
				score: 10
			},
			{
				score: 20
			},
			{
				score: 30
			}
		]
	}
];

function averageScore(players){
	let i, n, sum;
	sum = 0;
	for(i=0, n=players.length; i < n; i++){
		console.log('>>>>>> averageScore',n)
		sum += score(players[i]);
	}
	debugger;
	return sum / n;
};
function score(player){
	let i, n, sum;
	sum = 0;
	for(i=0, n=player.levels.length; i < n; i++){
		console.log('>>>>>> score',n)
		sum += player.levels[i].score;
	}
	debugger;
	return sum;
};

averageScore(players);

```

## 3. 避免為全域物件新增特性
web 瀏覽器中，全域物件會被 bind 到全域的 window 變數，而 window 變數是 `this` 關鍵字的初始值

```javascript
//範例8.3
this.foo; //undefined
foo = 'global foo';
this.foo; //"global foo"
```

```javascript
//範例8.4
var foo = 'global foo';
this.foo = 'changed';
foo; //"changed"
```

## 4. 使用全域物件來進行平台的功能偵測
全域物件不可或缺的用途是，可在執行環境中查詢平台上有哪些功能可用
例如：ES 5 引進 JSON 全域物件，假設部署程式碼的環境尚未提供 JSON 物件，則可測試這個全域物件是否存在，並提供一個替代的實作

```javascript
if(!this.JSON){
	this.JSON = {
		parse: ...,
		stringify: ...
	}
}
```

#### 補充
[MDN-Window](https://developer.mozilla.org/zh-TW/docs/Web/API/Window)
[MDN-Window.navigator](https://developer.mozilla.org/zh-TW/docs/Web/API/Window/navigator)
[MDN-透過用戶代理偵測瀏覽器](https://developer.mozilla.org/zh-TW/docs/Web/HTTP/Browser_detection_using_the_user_agent)

Window 物件提供很多 properties 可用，例如：透用用戶代理，偵測用戶設備有沒有觸控螢幕
```javascript
var hasTouchScreen = window.navigator.maxTouchPoints;
console.log(hasTouchScreen);
```

<!-- 以下為其他人整理 -->


## 可能導致的命名衝突
  全域變數在不同的作用域中計算，互相污染，導致結果值不如預期：
  ```javascript
  var i, sum;  //全域變數
  var amountArray = [
    [2, 2, 4],
    [5, 1, 2],
    [3, 1, 4]
  ];
  
  const totalAmount = total();
  console.log(totalAmount);   // 8

  function total() {
    sum = 0;
    for(i=0 ; i<amountArray.length ; i++) {
      sum += part(amountArray[i]);
      console.log(`i 目前的值為 ${i}, 總計算結果為： ${ sum }`);
    }
    return sum;
  }

  function part(item) {
    sum = 0;
    for(i=0 ; i<item.length ; i++) {
      sum += item[i];
      console.log(`第${i + 1}次計算結果為： ${ sum }`);
    }
    return sum;
  }
  ```

## 盡量將變數區域化
```javascript
var amountArray = [
    [2, 2, 4],
    [5, 1, 2],
    [3, 1, 4]
  ];
  
const totalAmount = total();
console.log(totalAmount);

function total() {
  var i, sum;  //全域變數
  sum = 0;
  for(i=0 ; i<amountArray.length ; i++) {
    sum += part(amountArray[i]);
    console.log(`i 目前的值為 ${i}, 總計算結果為： ${ sum }`);
  }
  return sum;
}

function part(item) {
  var i, sum;  //全域變數
  sum = 0;
  for(i=0 ; i<item.length ; i++) {
    sum += item[i];
    console.log(`第${i + 1}次計算結果為： ${ sum }`);
  }
  return sum;
}
```
## 功能偵測的應用
透過 this，可以查看宿主環境是否有提供該功能：
:::info
宿主環境為：
- 瀏覽器，this 為 window
- node.js，this 為 global
:::
```javascript
// polyfill 查看宿主環境有沒有提供 JSON 物件
// 如果沒有就在全域物件新增 JSON 物件，並寫入屬性
if(!this.JSON) {
  this.JSON = {
    parse: ...,
    stringify: ...
  }
}
```
## Question:
- 未繫結變數是什麼？
- 用 `var` 宣告的全域變數跟綁在全域物件上的變數，以及沒有用 var 宣告的變數，有差嗎？

建立全域變數很簡單，像這樣子：

```javascript
i = 0;
```

然後 i 就會被加到全域裡了 `gobal`，然後 `gobal` 這個空間是由一個 **全域物件** 被釋出，作為 `this` 的初始值，也會被繫結到全域的 `window` 變數（所以 `window` 在 `this` 下，但用 `this` 又可以存取到 `window`）：

```javascript
i = 0;
window.i;
this.i;
// 上方都會存取到全域 i

// 下方都會存取到 window 物件
this;
this.window;
```

**全域變數會汙染所有人共用的命名空間**，因為是所有人共有的嘛，所以可能導致名稱衝突：

```javascript
// 貼心小提醒，此範例會進入無窮迴圈，因為共用了全域變數 i

function doSomething() {
  i = 0;
  return i;
}

function aLoop() {
  for(i = 0; i <= 5; i += 1) {
    console.log(doSomething());
  }
}
```

上方的程式只要把變數放到 function 裡用 `var`、`let`、`const` 等宣告，`i`就不會是全域了：

```javascript
// 放心試吧，每一個 i 都在各自的作用域中被宣告

function doSomething() {
  const i = 0;
  return i;
}

function aLoop() {
  for(let i = 0; i <= 5; i += 1) {
    console.log(doSomething());
  }
}
```

## 可以用全域物件的地方

「動態反思（dynamically reflect）」，個人是沒查到 dynamically reflect 這個詞和 JavaScript 的關係（[查詢結果](https://www.google.com/search?sxsrf=ALeKk03rb9so8h7JXpoGpT0uG1X0XB9W3w%3A1594894895450&ei=LyoQX9SQG5KVr7wP2dS4-AM&q=dynamically+reflect+javascript&oq=dynamically+reflect+javascript&gs_lcp=CgZwc3ktYWIQAzIGCAAQDRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEAgQDRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEA0QBRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEAgQDRAeOgQIIxAnOgYIABAIEB46BQghEKABUOoHWN8YYM4ZaABwAHgAgAHqAYgBrQmSAQU5LjEuMpgBAKABAaoBB2d3cy13aXo&sclient=psy-ab&ved=0ahUKEwiU5Mm9xtHqAhWSyosBHVkqDj8Q4dUDCAw&uact=5)），可能是作者自己想的，不過他的意思應該是，直接從 `this` 或 `window` 去找自己想要的東西有沒有存在，如果沒有的話就自己加上去，以避免每個瀏覽器對某語法的支援度不同，導致程式出錯，範例是：

```javascript
// 如果 this 沒有有關 JSON 的 methods
if (!this.JSON) {
  // 就自己寫吧
  this.JSON = {
    parse: () => { /* ... */ },
    stringify: () => { /* ... */ },
  };
}
```

