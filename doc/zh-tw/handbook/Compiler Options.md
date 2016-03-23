## 編譯選項

選項                                     | 簡寫      | 描述
----------------------------------------|-----------|----------------------------------------------------------------------
`--declaration`                         | `-d`      | 生成相應的'.d.ts'檔案。
`--help`                                | `-h`      | 列印幫助資訊。
`--version`                             | `-v`      | 列印編譯器版本號。
`--module`                              | `-m`      | 指定生成哪個模組系統程式碼：'commonjs'，'amd'，'system'，或 'umd'。只有'amd'和'system'能和`--outFile`一起使用。
`--project`                             | `-p`      | 編譯指定目錄下的項目。這個目錄應該包含一個`tsconfig.json`檔案來管理編譯。檢視[tsconfig.json](./tsconfig.json.md)文件瞭解更多資訊。
`--target`                              | `-t`      | 指定ECMAScript目標版本'ES3' (預設)，'ES5'，或'ES6'<sup>[1]</sup>
`--watch`                               | `-w`      | 在監視模式下執行編譯器。會監視輸出檔案，在它們改變時重新編譯。
`--charset`                             |           | 輸入檔案的字符集。
`--diagnostics`                         |           | 顯示診斷資訊。
`--emitBOM`                             |           | 在輸出檔案的開頭加入BOM頭（UTF-8 Byte Order Mark）。
`--emitDecoratorMetadata`<sup>[1]</sup> |           | 給源碼裡的裝飾器聲明加上設計類型元資料。檢視[issue #2577](https://github.com/Microsoft/TypeScript/issues/2577)瞭解更多資訊。
`--inlineSourceMap`                     |           | 生成單個sourcemaps檔案，而不是將每sourcemaps生成不同的檔案。
`--inlineSources`                       |           | 將程式碼與sourcemaps生成到一個檔案中，要求同時設定了`--inlineSourceMap`屬性。
`--isolatedModules`                     |           | 無條件地給沒有解析的檔案生成imports。
`--jsx`                                 |           | 在'.tsx'檔案裡支援JSX：'React' 或 'Preserve'。檢視[JSX](./JSX.md)。
`--listFiles`                           |           | 編譯過程中列印檔名。
`--locale`                              |           | 顯示錯誤資訊時使用的語言，比如：en-us。
`--mapRoot`                             |           | 為偵錯程式指定指定sourcemap檔案的路徑，而不是使用生成時的路徑。當`.map`檔案是在執行時指定的，並不同於`js`檔案的地址時使用這個標記。指定的路徑會嵌入到`sourceMap`裡告訴偵錯程式到哪裡去找它們。
`--moduleResolution`<sup>[1]</sup>      |           | 決定如何處理模組。或者是'node'對於Node.js/io.js，或者是'classic'（預設）。
`--newLine`                             |           | 當生成檔案時指定行結束符：'CRLF'（dos）或 'LF' （unix）。
`--noEmit`                              |           | 不生成輸出檔案。
`--noEmitOnError`                       |           | 報錯時不生成輸出檔案。
`--noEmitHelpers`                       |           | 不在輸出檔案中生成使用者自定義的幫助函數程式碼，如`__extends`。
`--noImplicitAny`                       |           | 在表示式和聲明上有隱含的'any'類型時報錯。
`--noLib`                               |           | 不包含預設的庫檔案（lib.d.ts）。
`--noResolve`                           |           | 不把`/// <reference``>`或模組匯入的檔案加到編譯檔案列表。
`--out`                                 |           | 棄用。使用 `--outFile` 代替。
`--outDir`                              |           | 重定向輸出目錄。
`--outFile`                             |           | 將輸出檔案合併為一個檔案。合併的順序是根據傳入編譯器的檔案順序和`///<reference``>`和`import`的檔案順序決定的。檢視輸出檔案順序檔案瞭解詳情。
`--preserveConstEnums`                  |           | 保留`const`和`enum`聲明。檢視[const enums documentation](https://github.com/Microsoft/TypeScript/blob/master/doc/spec.md#94-constant-enum-declarations)瞭解詳情。
`--removeComments`                      |           | 刪除所有註釋，除了以`/!*`開頭的版權資訊
`--rootDir`                             |           | 僅用來控制輸出的目錄結構`--outDir`。
`--sourceMap`                           |           | 生成相應的'.map'檔案。
`--sourceRoot`                          |           | 指定TypeScript原始檔的路徑，以便偵錯程式定位。當TypeScript檔案的位置是在執行時指定時使用此標記。路徑資訊會被加到`sourceMap`裡。
`--stripInternal`<sup>[1]</sup>         |           | 不對具有`/** @internal */` JSDoc註解的程式碼生成程式碼。
`--suppressImplicitAnyIndexErrors`      |           | 阻止`--noImplicitAny`對缺少索引簽名的索引物件報錯。檢視[issue #1232](https://github.com/Microsoft/TypeScript/issues/1232#issuecomment-64510362)瞭解詳情。

<sup>[1]</sup> 這些選項是試驗性的。

## 相關資訊

* 對於 tsconfig.json 請檢視 [tsconfig.json](./tsconfig.json.md)
* 針對如何在MSBuild項目裡使用編譯器選項請檢視[在MSBuild項目裡設定編譯器選項](./Compiler Options in MSBuild.md)
