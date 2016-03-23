這個快速上手指南將會教你如何將TypeScript和[React](http://facebook.github.io/react/)還有[webpack](http://webpack.github.io/)連結在一起使用。

我們假設已經在使用[Node.js](https://nodejs.org/)和[npm](https://www.npmjs.com/)。

# 初始化項目結構

讓我們新建一個目錄。
將會命名為`proj`，但是你可以改成任何你喜歡的名字。

```shell
mkdir proj
cd proj
```

我們會像下面的結構組織我們的工程：

```text
proj/
   +- src/
   |    +- components/
   |
   +- dist/
```

TypeScript檔案會放在`src`資料夾裡，通過TypeScript編譯器編譯，然後經webpack處理，最後生成一個`bundle.js`檔案放在`dist`目錄下。
我們自定義的元件將會放在`src/components`資料夾下。

下面來建立基本結構：

```shell
mkdir src
cd src
mkdir components
cd ..
mkdir dist
```

# 初始化工程

現在把這個目錄變成npm包。

```shell
npm init
```

你會看到一些提示。
你可以使用預設項除了開始指令碼。
使用`./lib/bundle.js`做為開始指令碼。
當然，你也可以隨時到生成的`package.json`檔案裡修改。

# 安裝依賴

首先確保TypeScript，typings和webpack已經全局安裝了。

```shell
npm install -g typescript typings webpack
```

Webpack這個工具可以將你的所有程式碼和可選擇地將依賴捆綁成一個單獨的`.js`檔案。
[Typings](https://www.npmjs.com/package/typings)是一個包管理器，它是用來獲取定義檔案的。

現在我們新增React和React-DOM依賴到`package.json`檔案裡：

```shell
npm install --save react react-dom
```

接下來，我們要新增開發時依賴[ts-loader](https://www.npmjs.com/package/ts-loader)和[source-map-loader](https://www.npmjs.com/package/source-map-loader)。

```shell
npm install --save-dev ts-loader source-map-loader
npm link typescript
```

這些依賴會讓TypeScript和webpack在一起良好地工作。
ts-loader可以讓webpack使用TypeScript的標準配置檔案`tsconfig.json`編譯TypeScript程式碼。
source-map-loader使用TypeScript輸出的sourcemap檔案來告訴webpack何時生成*自己的*sourcemaps。
這就允許你在偵錯最終生成的檔案時就好像在偵錯TypeScript源碼一樣。

連結TypeScript，允許ts-loader使用全局安裝的TypeScript，而不需要單獨的本地拷貝。
如果你想要一個本地的拷貝，執行`npm install typescript`。

最後，我們使用`typings`工具來獲取React的聲明檔案：

```shell
typings install --ambient --save react
typings install --ambient --save react-dom
```

`--ambient`標記告訴typings從[DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)獲取聲明檔案，這是由社群維護的`.d.ts`檔案倉庫。
這個命令會建立一個名為`typings.json`的檔案和一個`typings`目錄在當前目錄下。

# 寫一些程式碼

下面使用React寫一段TypeScript程式碼。
首先，在`src/components`目錄下建立一個名為`Hello.tsx`的檔案，程式碼如下：

```ts
import * as React from "react";
import * as ReactDOM from "react-dom";

export class HelloComponent extends React.Component<any, any> {
    render() {
        return <h1>Hello from {this.props.compiler} and {this.props.framework}!</h1>;
    }
}

```

注意一點這個例子已經很像類了，我們不再需要使用類。
使用React的其它方式（比如[無狀態的功能元件](https://facebook.github.io/react/docs/reusable-components.html#stateless-functions)）。

接下來，在`src`下建立`index.tsx`檔案，源碼如下：

```ts
import * as React from "react";
import * as ReactDOM from "react-dom";

import { HelloComponent } from "./components/Hello";

ReactDOM.render(
    <HelloComponent compiler="TypeScript" framework="React" />,
    document.getElementById("example")
);
```

我們僅僅將`Hello`元件匯入`index.tsx`。
注意，不同於`"react"`或`"react-dom"`，我們使用`index.tsx`的*相對路徑* - 這很重要。
如果不這樣做，TypeScript只會嘗試在`node_modules`資料夾裡查詢。
其它使用React的方法也應該可以。

我們還需要一個頁面來顯示`Hello`元件。
在根目錄`proj`建立一個名為`index.html`的檔案，如下：

```html
<!DOCTYPE html>
<html>
    <head>
        <meta charset="UTF-8" />
        <title>Hello React!</title>
    </head>
    <body>
        <div id="example"></div>

        <!-- Dependencies -->
        <script src="./node_modules/react/dist/react.js" />
        <script src="./node_modules/react-dom/dist/react-dom.js" />

        <!-- Main -->
        <script src="./dist/bundle.js"></script>
    </body>
</html>
```

需要注意一點我們是從`node_modules`引入的檔案。
React和React-DOM的npm包裡包含了獨立的`.js`檔案，你可以在頁面上引入它們，這裡我們為了快捷就直接引用了。
可以隨意地將它們拷貝到其它目錄下，或者從CDN上引用。
Facebook在CND上提供了一系列可用的React版本，你可以在這裡檢視[更多內容](http://facebook.github.io/react/downloads.html#development-vs.-production-builds)。

# 新增TypeScript配置檔案

現在，可以把所有TypeScript檔案放在一起 - 包括我們編寫的程式碼和必要的typings檔案。

現在需要建立`tsconfig.json`檔案，它包含輸入檔案的列表和編譯選項。
在根目錄下執行下在命令：

```shell
tsc --init ./typings/main.d.ts ./src/index.tsx --jsx react --outDir ./dist --sourceMap --noImplicitAny
```

你可以在[這裡](../tsconfig.json.md)學習到更多關於`tsconfig.json`。

# 建立webpack配置檔案

新建一個`webpack.config.js`檔案在工程根目錄下。

```js
module.exports = {
    entry: "./src/index.tsx",
    output: {
        filename: "./dist/bundle.js",
    },

    // Enable sourcemaps for debugging webpack's output.
    devtool: "source-map",

    resolve: {
        // Add '.ts' and '.tsx' as resolvable extensions.
        extensions: ["", ".webpack.js", ".web.js", ".ts", ".tsx", ".js"]
    },

    module: {
        loaders: [
            // All files with a '.ts' or '.tsx' extension will be handled by 'ts-loader'.
            { test: /\.tsx?$/, loader: "ts-loader" }
        ],

        preLoaders: [
            // All output '.js' files will have any sourcemaps re-processed by 'source-map-loader'.
            { test: /\.js$/, loader: "source-map-loader" }
        ]
    }，

    // When importing a module whose path matches one of the following, just
    // assume a corresponding global variable exists and use that instead.
    // This is important because it allows us to avoid bundling all of our
    // dependencies, which allows browsers to cache those libraries between builds.
    externals: {
        "react": "React",
        "react-dom": "ReactDOM"
    },
};
```

大家可能對`externals`欄位有所疑惑。
我們想要避免把所有的React都放到一個檔案裡，因為會增加編譯時間並且瀏覽器還能夠快取沒有發生改變的庫檔案。
理想情況下，我們只需要在瀏覽器裡引入React模組，但是大部分瀏覽器還沒有支援模組。
因此大部分程式碼庫會把自己包裹在一個單獨的全局變數內，比如：`jQuery`或`_`。
這叫做“名稱空間”模式，webpack也允許我們繼續使用通過這種方式寫的程式碼庫。
通過我們的設定`"react": "React"`，webpack會神奇地將所有對`"react"`的匯入轉換成從`React`全局變數中載入。

你可以在[這裡](http://webpack.github.io/docs/configuration.html)瞭解更多如何配置webpack。

# 整合在一起

執行：

```shell
webpack
```

在瀏覽器裡開啟`index.html`，工程應該已經可以用了！
你可以看到頁面上顯示著“Hello from TypeScript and React!”
