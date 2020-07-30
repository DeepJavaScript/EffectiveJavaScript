# 條款 08 盡量少用全域物件
摘要
1. 避免為全域物件新增特性
2. 避免宣告全域變數
3. 盡量將變數宣告在 black scope 
4. 使用全域物件來進行平台的功能偵測

## 1. 避免為全域物件新增特性
建立全域變數很簡單，要存取全域變數，除了用變數本身，還可以透過 `window` 來存取值
```javascript
i = 0;
window.i;

var foo = 'global foo';
window.foo; //'global foo'
```


變數 i、foo 會被加到全域 `gobal` 裡。`gobal` 這個空間會以 **全域物件** 被釋出，作為 `this` 的初始值，可在程式最頂層取用它。在 web 瀏覽器中，這個 **全域物件** 會被繫結到全域的 `window` 變數
```javascript
i = 0;
this.i;

this.foo; //undefined
foo = 'global foo';
this.foo; //"global foo"

this.window.window
```


以下是 `全域物件` 的規範
>摘自 [whatwg-Creating browsing contexts](https://html.spec.whatwg.org/multipage/browsers.html#creating-browsing-contexts)
>
>7.1.1 Creating browsing contexts
>
>7. Let realm execution context be the result of creating a new JavaScript realm given agent and the following customizations:
>- For the global object, create a new Window object.
>- For the global this binding, use browsingContext's WindowProxy object.


#### 補充
不同宿主環境存取 `全域物件` 的變數名稱各有不同，像瀏覽器是 `window` 而 node `global`，現在我們可以使用 `globalThis` 關鍵字，在瀏覽器或 node 環境裡都可以存取 `全域物件` 

[tc39-globalThis](https://github.com/tc39/proposal-global)


## 2. 避免宣告全域變數 & 3. 盡量將變數宣告在 black scope 
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

## 4. 使用全域物件來進行平台的功能偵測
全域物件不可或缺的用途是，可在執行環境中查詢平台上有哪些功能可用
例如：ES 5 引進 JSON 全域物件，假設部署程式碼的環境尚未提供 JSON 物件，則可測試這個全域物件是否存在，並提供一個替代的實作

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

動態反思（dynamically reflect）」，個人是沒查到 dynamically reflect 這個詞和 JavaScript 的關係（[查詢結果](https://www.google.com/search?sxsrf=ALeKk03rb9so8h7JXpoGpT0uG1X0XB9W3w%3A1594894895450&ei=LyoQX9SQG5KVr7wP2dS4-AM&q=dynamically+reflect+javascript&oq=dynamically+reflect+javascript&gs_lcp=CgZwc3ktYWIQAzIGCAAQDRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEAgQDRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEA0QBRAeMggIABAIEA0QHjIICAAQCBANEB4yCAgAEAgQDRAeOgQIIxAnOgYIABAIEB46BQghEKABUOoHWN8YYM4ZaABwAHgAgAHqAYgBrQmSAQU5LjEuMpgBAKABAaoBB2d3cy13aXo&sclient=psy-ab&ved=0ahUKEwiU5Mm9xtHqAhWSyosBHVkqDj8Q4dUDCAw&uact=5)），可能是作者自己想的，不過他的意思應該是，直接從 `this` 或 `window` 去找自己想要的東西有沒有存在，如果沒有的話就自己加上去，以避免每個瀏覽器對某語法的支援度不同，導致程式出錯

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

