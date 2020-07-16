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

