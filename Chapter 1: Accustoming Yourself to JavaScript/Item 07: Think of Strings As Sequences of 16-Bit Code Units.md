# 條款 07 把字串想成是 16 位元編碼單位的序列

- JS 的字串是由 16 位元的 code units 所構成，而不是 Unicode 的 code points 所構成
- Unicode 第 2<sup>16</sup> 個和以上的 code points 在 JS 中是以兩個 code units 來表示，稱為一個 surrogate pair
- surrogate pair 會使字串元素的計數出錯，會影響 `length`、`charAt`、`charCodeAt` 和 regex pattern，例如：`.`
- 建議使用第三方 library 處理 code point 的字串操作
- 每當使用會作用在字串上的 library 時，請參閱文件，以了解它是如何處理全範圍的 code points

---

JS 字串中的每個元素都是一個 16 位元的值，每個元素都對應到單一個 code point (一個元素就是一個 16 位元的 code unit)。

例如：下面 Unicode 字串中編碼元素的 index 與 code point 的 index 完全相同：

![code points](https://i.imgur.com/SXiASBr.png)

例如：`𝄞 clef` 字串實際上包含 6 個 code point，但 `length` 為 7：

```javascript
console.log("𝄞 clef".length);  // 7
console.log("G clef".length);  // 6
```

因為音符中的 G 音譜號 (musical G clef symbol，"𝄞") 被指定的 code point 是 U+1D11E (code point 編號 119070 的 16 進位慣用表示法)，而它在 UTF-16 中則是以 0xd834 及 0xdd1e 這一對 code units 來表示。

![surrogate pairs](https://i.imgur.com/PrNrqVc.png)

Surrogate pairs 可確保不會與任一個有效的 BMP code point 發生混淆，使 code units 的索引會與 code points 的索引不同。

每一個 code point 的 UTF-16 編碼可能會需要一個或兩個 16 位元的 code unit，所以 UTF-16 是一種 variable-length (長度可變動的) 編碼。記憶體中一個長度為 n 的字串所需的儲存空間會隨著字串中所用的特定 code points 而變。通常會需要從字串的開頭開始找起，相對比常數查找較花時間。

中文字的範例：[`𧺯` (0xD85F 0xDEAF)](https://www.unicode.org/cgi-bin/GetUnihanData.pl?codepoint=27EAF)，[`﨣` (0xFA23)](https://www.unicode.org/cgi-bin/GetUnihanData.pl?codepoint=FA23) (應該是念 ㄐ一ㄡ 吧？)

```javascript
let char1 = '﨣';
let char2 = '𧺯';
console.log(char1 == char2);       // false
console.log(char1.length);         // 1
console.log(char2.length);         // 2
console.log(char1.charCodeAt(0));  // 64035
console.log(char2.charCodeAt(0));  // 55391
```

資料來源：[中日韓統一表意文字擴充區B - Wikipedia](https://zh.wikipedia.org/wiki/中日韓統一表意文字擴展區B)

擷取字串中的元素會得到 code unit，而不是 code point：

```javascript
console.log("𝄞 clef".charCodeAt(0));      // 55348 (0xd834)
console.log("𝄞 clef".charCodeAt(1));      // 56606 (0xdd1e)
console.log("𝄞 clef".charAt(1) === " ");  // false
console.log("𝄞 clef".charAt(2) === " ");  // true
```

regex 也是：

```javascript
console.log(/^.$/.test("𝄞"));   // false
console.log(/^..$/.test("𝄞"));  // true
```

Unicode 在擴充前，JavaScript 已經決定使用 16 位元的字串元素，例如 `length`、`charAt` 與 `charCodeAt` 都是在 code units 的層次運作的，而非在 code points 層次。所以每當一個字串含有來自 supplementary planes 的 code points 時，JavaScript 就會把每一個 code points 表示為兩個元素，也就是 code point 的 UTF-16 surrogate pair，而非表現為一個元素。

JavaScript 的字串行為很像 UTF-16 code units 所構成的序列。雖然 JS 內建的字串資料型別作用在 code units 的層次，但不代表 API 無法察覺到 code points 與 surrogate pairs 的存在。

要支援全範圍 Unicode 很麻煩，所以不能依賴以下東西來處理：
- 字串方法
- `length` 的值
- 透過 index 查找
- regex pattern

但有些標準 ECMAScript library 能正確地處理 surrogate pairs，例如操作 URI 的 `encodeURI`、`decodeURI`、`encodeURIComponent` 和 `decodeURIComponent`。

書中提到：若要處理的範圍超出 BMP，建議找現有的 library 幫你處理，而非自己處理，因為要正確地處理編碼和解碼的細節很困難。

額外補充：在 [tc39/proposal-regexp-unicode-sequence-properties](https://github.com/tc39/proposal-regexp-unicode-sequence-properties) (在 Unicode property 轉義中支援序列 property) 提案 (以納入 ES9 規範) 有一個新功能可以解決 regex 的問題：

```javascript
console.log(/^.$/u.test("𝄞"));  // true
```

## code space

- 一系列用來編碼字元的數值
- 以 Unicode 來說：Unicode code space 是從 0 ~ 10FFFF 的整數範圍

## code point (或稱 code position)

- 任何編碼字元集合 (coded character set) 中的字元值或字元位置
- code space 中的任何值，但不是所有 code point 都分配給編碼字元
  - ASCII 包含 0 ~ FF 的 128 個 code point
  - Unicode 包含 0 ~ 10FFFF 的 1114112 個 code point
- 很多編碼的 code point 代表一個字元，但也有可能是其他涵義 (例如：用於格式化)
- code point 分成兩種：
  - 來自編碼的數字 (依 bit 的序列)
    - 以不同方式編碼特定的 code space
  - 特定 graphical representation (圖形表示形式，glyph) 的抽象字元
    - 透過不同的 glyph 來顯示字元

世界上所有書寫系統的每一個文字單元都被指定一個獨特的整數，介於 0 與 1,114,111 之間，在 Unicode 術語中被稱為一個 code point (編碼位置，或稱「碼位」、「代碼點」)，與 ASCII 沒有太大差異。

ASCII 把每個索引對映 (map) 到單一個獨特的二元表示值 (binary representation)，而 Unicode 允許 code points 有多個不同的二元編碼 (binary encodings)。不同的編碼會在一個字串所需的儲存空間量以及運算 (例如索引至一個字串內) 的速度之間做出取捨。多種格式例如：UTF-8、UTF-16 與 UTF-32。

## Plane

Unicode spec 定義 plane 是 65536 (即 2<sup>16</sup> ) 個 code point。

Unicode code space 分成 17 個 [plane (平面)](https://en.wikipedia.org/wiki/Plane_(Unicode))，所以 Unicode code space 的總大小為 17 x 65536 = 1114112
- plane 0：BMP (Basic Multilingual Plane，基本多文種平面)：編碼從 U+0000 至 U+FFFF
- plane 1~16：supplementary planes (補充平面)：編碼從 U+10000 至 U+10FFFF
  - plane 1：SMP (Supplementary Multilingual Plane，多文種補充平面)：擺放拼音文字 (主要為現時已不再使用的古老文字)、手寫文字、音符、繪文字和其他圖形符號。用於學者的專業論文中使用的古老或過時的語言書寫符號，以及網路通信等使用的表情符號

![Unicode](https://i.imgur.com/29G46kB.png)

## UCS-2

UCS 是 [Universal Coded Character Set](https://en.m.wikipedia.org/wiki/Universal_Coded_Character_Set) 的縮寫。

- UCS-2 使用 single code value，每個 code value 代表一個 code point
- 每個字元的取值範圍在 0 ~ 65535 之間，每個字元需要 2 byte (16 bit)
- 所以 UCS-2 可對前 65536 個 code point 進行編碼 (即 Basic Multilingual Plane，BMP)

UCS-2 一開始採用每個 code point 都能夠放入一個 16 位元數字中，一對一的對映方式，這種編碼元素被稱為 code units (編碼單位)。其中每一個 code unit 都對映到單一個 Unicode code point。

主要好處是，索引至一個字串內 (indexing into a string) 只需常數時間的運算 (時間複雜度：`O(n)` )。要存取一個字串的第 n 個 code point，單純只要選擇陣列的第 n 個 16 位元元素。

但 code point 的範圍被擴充後，UCS-2 變過時了。

UTF-16 多了 surrogate pairs：成對的 16 位元 code unit 被一起用來編碼 2<sup>16</sup> 個以上的每個 code points。

例如，前面提到的 G 音譜號 (musical G clef symbol，"𝄞") 就是 code point 的 UTF-16 surrogate pair。

## 資料來源

- [Code point - Wikipedia](https://en.wikipedia.org/wiki/Code_point)
- [Unicode 字元平面對映 - Wikipedia](https://zh.wikipedia.org/wiki/Unicode字符平面映射)
- [Unicode - Wikipedia](https://zh.wikipedia.org/wiki/Unicode)
- [Unicode 與 JavaScript字串 | iThome](https://www.ithome.com.tw/voice/131688)
