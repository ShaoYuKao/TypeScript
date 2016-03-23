與ASP.NET v5一起使用TypeScript需要你用特定的方式來設定你的工程。
更多關於ASP.NET v5的詳細資訊請檢視[ASP.NET v5 文件](http://docs.asp.net/en/latest/conceptual-overview/index.html)
在Visual Studio的工程裡支援當前的tsconfig.json還在開發之中，可以在這裡檢視進度[#3983](https://github.com/Microsoft/TypeScript/issues/3983)。

# 工程設定

我們就以在Visual Studio 2015裡建立一個空的ASP.NET v5工程開始，如果你對ASP.NET v5還不熟悉，可以檢視[這個教程](http://docs.asp.net/en/latest/tutorials/your-first-aspnet-application.html)。

![新建立一個空的工程](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/new-project.png)

然後在工程根目錄下新增一個`scripts`目錄。
這就是我們將要新增TypeScript檔案和[`tsconfig.json`](tsconfig.json.md)檔案來設定編譯選項的地方。
請注意目錄名和路徑都必須這樣才能正常工作。
新增`tsconfig.json`檔案，右鍵點選`scripts`目錄，選擇`Add`，`New Item`。
在`Client-side`下，你能夠找到它，如下所示。

![在 Visual Studio 裡新增'tsconfig.json'檔案](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/add-tsconfig.png)

![A project in Visual Studio's Solution Explorer](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/project.png)

最後我們還要將下面的選項新增到`tsconfig.json`檔案的`"compilerOptions"`節點裡，讓編譯器輸出重定向到`wwwroot`資料夾：

```json
"outDir": "../wwwroot/"
```

下面是配置好`tsconfig.json`後可能的樣子

```json
{
    "compilerOptions": {
        "noImplicitAny": false,
        "noEmitOnError": true,
        "removeComments": false,
        "sourceMap": true,
        "target": "es5",
        "outDir": "../wwwroot"
    }
}
```

現在如果我們構建這個工程，你就會注意到`app.js`和`app.js.map`檔案被建立在`wwwroot`目錄裡。

![構建後的檔案](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/postbuild.png)

## 工程與虛擬工程

當添加了一個`tsconfig.json`檔案，你要明白很重要的一點是我們建立了一個虛擬TypeScript工程，在包含`tsconfig.json`檔案的目錄下。
被當作這個虛擬工程一部分的TypeScript檔案是不會在儲存的時候編譯的。
在包含`tsconfig.json`檔案的目錄*外層*裡存在的TypeScript檔案*不會*被當作虛擬工程的一部分。
下圖中，可以見到這個虛擬工程，在紅色矩形裡。

![A virtual project in Visual Studio's Solution Explorer](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/virtual-project.png)

## 儲存時編譯

想要啟用ASP.NET v5項目的*儲存時編譯*功能，你必須為不是虛擬TypeScript工程一部分的TypeScript檔案啟用*儲存時編譯*功能。
如果工程裡存在`tsconfig.json`檔案，那麼模組類型選項的設定會被忽略。

![Compile on Save](https://raw.githubusercontent.com/wiki/Microsoft/TypeScript/aspnet-screenshots/compile-on-save.png)
