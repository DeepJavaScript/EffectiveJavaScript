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
"𝄞 clef".length;  // 7
"G clef".length;  // 6
```

擷取字串中的元素會得到 code unit，而不是 code point：

```javascript
"𝄞 clef".charCodeAt(0);      // 55348 (0xd834)
"𝄞 clef".charCodeAt(1);      // 56606 (0xdd1e)
"𝄞 clef".charAt(1) === " ";  // false
"𝄞 clef".charAt(2) === " ";  // true
```

regex 也是：

```javascript
/^.$/.test("𝄞");   // false
/^..$/.test("𝄞");  // true
```

補充：在 [tc39/proposal-regexp-unicode-sequence-properties](https://github.com/tc39/proposal-regexp-unicode-sequence-properties) (在 Unicode property 轉義中支援序列 property) 提案 (以納入 ES9 規範) 有一個新功能可以解決 regex 的問題：

```javascript
/^.$/u.test("𝄞");  // true
```

雖然 JS 內建的字串資料型別作用在 code units 的層次，但不代表 API 無法察覺到 code points 與 surrogate pairs 的存在。

因為有些標準 ECMAScript library 能正確地處理 surrogate pairs，例如操作 URI 的 `encodeURI`、`decodeURI`、`encodeURIComponent` 和 `decodeURIComponent`。

> 若某個 JS 環境提供作用在字串上的 library，例如操作網頁內容、使用字串進行 I/O，建議查官方文件了解它是如何處理全範圍的 Unicode code points。