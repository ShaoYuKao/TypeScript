<!-- markdownlint-disable MD029 -->TypeScript編譯器處理nodejs模組名時使用的是[Node.js模組解析演算法](https://nodejs.org/api/modules.html#modules_all_together)。
TypeScript編譯器可以同時載入與npm包綁在一起的類型資訊。
編譯通過下面的規則來查詢`"foo"`模組的類型資訊：

1. 嘗試載入相應程式碼包目錄下`package.json`檔案（`node_modules/foo/`）。

如果存在，從`"typings"`欄位裡讀取類型檔案的路徑。比如，在下面的`package.json`裡，編譯器會認為類型檔案位於`node_modules/foo/lib/foo.d.ts`。

```JSON
{
    "name": "foo",
    "author": "Vandelay Industries",
    "version": "1.0.0",
    "main": "./lib/foo.js",
    "typings": "./lib/foo.d.ts"
}
```

2. 嘗試載入在相應程式碼包目錄下的名字為`index.d.ts`的檔案（`node_modules/foo/`） - 這個檔案應該包含了這個程式碼包的類型資訊。

解析模組的詳細演算法可以在[這裡](https://github.com/Microsoft/TypeScript/issues/2338)找到。

### 類型資訊檔案應該是什麼樣子的

* 是一個`.d.ts`檔案
* 是一個外部模組
* 不包含`///<reference>`引用

基本的原理是類型檔案不能引入新的可編譯程式碼；
否則真正的實現檔案就可能會在編譯時被重蓋。
另外，**載入類型資訊不應該汙染全局空間**，當從同一個庫的不同版本中引入潛在衝突的實體的時候。