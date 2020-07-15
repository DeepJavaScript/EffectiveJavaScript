# 條款 07 把字串想成是 16 位元編碼單位的序列

(跳過)

整理：

- JS 的字串是由 16 位元的 code units 所構成，而不是 Unicode 的 code points 所構成
- Unicode 第 $2^{16}$ 個和以上的 code points 在 JS 中是以兩個 code units 來表示，稱為一個 surrogate pair
- surrogate pair 會使字串元素的計數出錯，會影響 `length`、`charAt`、`charCodeAt` 和 regex pattern，例如：`.`
- 建議使用第三方 library 處理 code point 的字串操作
- 每當使用會作用在字串上的 library 時，請參閱文件，以了解它是如何處理全範圍的 code points

- 每個 code point 都放在一個 16 位元數字中
- 把 code point map 至單一個 Unicode code point

JS 字串中的每個元素都是一個 16 位元的值，每個元素都對應到單一個 code point (一個元素就是一個 16 位元的 code unit)

要支援全範圍 Unicode 很麻煩，因為不能依賴以下東西來處理：

- 字串方法
- `length` 的值
- 透過 index 查找
- regex pattern

若要處理的範圍超出 BMP，建議找現有的 library 幫你處理，而非自己處理，因為要正確地處理編碼和解碼的細節很困難。

JS 的字串行為很像 UTF-16 code units 所構成的序列。

例如：`𝄞 clef` 字串實際上包含 6 個 code point，但 `length` 為 7：

```javascript
"𝄞 clef".length; // 7
"G clef".length; // 6
```

擷取字串中的元素會得到 code unit，而不是 code point：

```javascript
"𝄞 clef".charCodeAt(0); // 55348 (0xd834)
"𝄞 clef".charCodeAt(1); // 56606 (0xdd1e)
"𝄞 clef".charAt(1) === " "; // false
"𝄞 clef".charAt(2) === " "; // true
```

regex 也是：

```javascript
/^.$/.test("𝄞"); // false
/^..$/.test("𝄞"); // true
```

補充：在 [tc39/proposal-regexp-unicode-sequence-properties](https://github.com/tc39/proposal-regexp-unicode-sequence-properties) (在 Unicode property 轉義中支援序列 property) 提案 (以納入 ES9 規範) 有一個新功能可以解決 regex 的問題：

```javascript
/^.$/u.test("𝄞"); // true
```

雖然 JS 內建的字串資料型別作用在 code units 的層次，但不代表 API 無法察覺到 code points 與 surrogate pairs 的存在。

因為有些標準 ECMAScript library 能正確地處理 surrogate pairs，例如操作 URI 的 `encodeURI`、`decodeURI`、`encodeURIComponent` 和 `decodeURIComponent`。

> 若某個 JS 環境提供作用在字串上的 library，例如操作網頁內容、使用字串進行 I/O，建議查官方文件了解它是如何處理全範圍的 Unicode code points。

世界上所有書寫系統的每一個文字單元都被指定一個獨特的整數，介於 0 與 1,114,111 之間，在 Unicode 術語中被稱為一個 code point (編碼位置，或稱「碼位」、「代碼點」)，與 ASCII 沒有太大差異。

ASKII 把每個索引對映 (map) 到單一個獨特的二元表示值 (binary representation)，而 Unicode 允許 code points 有多個不同的二元編碼 (binary encodings)。不同的編碼會在一個字串所需的儲存空間量以及運算 (例如索引至一個字串內) 的速度之間做出取捨。多種格式例如：UTF-8、UTF-16 與 UTF-32。

UCS-2 一開始採用每個 code point 都能夠放入一個 16 位元數字中，一對一的對映方式，這種編碼元素被稱為 code units (編碼單位)。其中每一個 code unit 都對映到單一個 Unicode code point。主要好處是，索引至一個字串內 (indexing into a string) 只需常數時間的運算。要存取一個字串的第 n 個 code point，單純只要選擇陣列的第 n 個 16 位元元素。

Unicode 字串中編碼元素的索引與 code point 的索引是完全相同的。

![code points](https://i.imgur.com/SXiASBr.png)

[Unicode 字元平面對映](https://zh.wikipedia.org/wiki/Unicode%E5%AD%97%E7%AC%A6%E5%B9%B3%E9%9D%A2%E6%98%A0%E5%B0%84)

目前的 Unicode 字元分為 17 組編排，每組稱為平面 (Plane)，而每平面擁有 65536（即 2^16^）個代碼點。然而目前只用了少數平面。

![Unicode](https://i.imgur.com/29G46kB.png)

基本多文種平面 (Basic Multilingual Plane, BMP)，或稱第 0 平面或 0 號平面(Plane 0)，是 Unicode 中的一個編碼區段。編碼從 U+0000 至 U+FFFF。

第一輔助平面又稱多文種補充平面 (Supplementary Multilingual Plane，縮寫 SMP，或簡稱 Plane 1)，擺放拼音文字 (主要為現時已不再使用的古老文字)、手寫文字、音符、繪文字和其他圖形符號。用於學者的專業論文中使用的古老或過時的語言書寫符號，以及網路通信等使用的表情符號。

code points 的範圍被擴充後，加入了代理對組 (surrogate pairs)，成對的 16 位元 code units 被一起用來編碼 2^16^ 個以上的每一個 code points。

舉例來說，音符中的 G 音譜號 (musical G clef symbol，"𝄞") 被指定的 code point 是 U+1D11E (code point 編號 119,070 的 16 進位慣用表示法)，而它在 UTF-16 中則是以 0xd834 及 0xddle 這一對 code units 來表示。

代理對組可確保不會與任一個有效的 BMP code point 發生混淆，這使得 code units 的索引會與 code points 的索引不同。

![surrogate pairs](https://i.imgur.com/PrNrqVc.png)

每一個 code point 的 UTF-16 編碼可能會需要一個或兩個 16 位元的 code unit，所以 UTF-16 是一種 variable-length (長度可變動的) 編碼。記憶體中一個長度為 n 的字串所需的儲存空間會隨著字串中所用的特定 code points 而變。通常會需要從字串的開頭開始找起，相對比常數查找較花時間。

Unicode 擴充前，javaxcript 已經決定使用 16 位元的字串元素，例如 length、charAt 與 charCodeAt 都是在 code units 的層次運作的，而非在 code points 層次。所以每當一個字串含有來自 supplementary plans 的 code points 時，javascript 就會把每一個 code points 表示為兩個元素，也就是 code point 的 UTF-16 surrogate pair，而非表現為一個元素。

Javascript 字串中的一個元素 (element) 就是一個 16 位元的 code unit。JS 引擎內部可能會最佳化字串內容的儲存。

==正規表達式需額外留意。==

```javascript=
"𝄞".charCodeAt(0); //55348 (0xd834)
"𝄞".charCodeAt(1); //56606 (0xddle)
"𝄞".length; // 2
```
