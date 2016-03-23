> **關於術語的一點說明:**
請務必注意一點，TypeScript 1.5裡術語名已經發生了變化。
“內部模組”現在稱做“名稱空間”。
“外部模組”現在則簡稱為“模組”，這是為了與[ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/)裡的術語保持一致，(也就是說 `module X {` 相當於現在推薦的寫法 `namespace X {`)。

# 介紹

從ECMAScript 2015開始，JavaScript引入了模組的概念。TypeScript也沿用這個概念。

模組在其自身的作用域裡執行，而不是在全局作用域裡；這意味著定義在一個模組裡的變數，函數，類等等在模組外部是不可見的，除非你明確地使用[`export`形式](#export)之一匯出它們。
相反，如果想使用其它模組匯出的變數，函數，類，介面等的時候，你必須要匯入它們，可以使用[`import`形式](#import)之一。

模組是自聲明的；兩個模組之間的關係是通過在檔案級別上使用imports和exports建立的。

模組使用模組載入器去匯入其它的模組。
在執行時，模組載入器的作用是在執行此模組程式碼前去查詢並執行這個模組的所有依賴。
大家最熟知的JavaScript模組載入器是服務於Node.js的[CommonJS](https://en.wikipedia.org/wiki/CommonJS)和服務於Web應用的[Require.js](http://requirejs.org/)。

TypeScript與ECMAScript 2015一樣，任何包含頂級`import`或者`export`的檔案都被當成一個模組。

# <a name="export"></a>匯出

## 匯出聲明

任何聲明（比如變數，函數，類，類型別名或介面）都能夠通過新增`export`關鍵字來匯出。

##### Validation.ts

```ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

##### ZipCodeValidator.ts

```ts
export const numberRegexp = /^[0-9]+$/;

export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
```

## 匯出語句

匯出語句很便利，因為我們可能需要對匯出的部分重新命名，所以上面的例子可以這樣改寫：

```ts
class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export { ZipCodeValidator };
export { ZipCodeValidator as mainValidator };
```

## 重新匯出

我們經常會去擴充套件其它模組，並且只匯出那個模組的部分內容。
重新匯出功能並不會在當前模組匯入那個模組或定義一個新的局部變數。

##### ParseIntBasedZipCodeValidator.ts

```ts
export class ParseIntBasedZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && parseInt(s).toString() === s;
    }
}

// 匯出原先的驗證器但做了重新命名
export {ZipCodeValidator as RegExpBasedZipCodeValidator} from "./ZipCodeValidator";
```

或者一個模組可以包裹多個模組，並把他們匯出的內容聯合在一起通過語法：`export * from "module"`。

##### AllValidators.ts

```ts
export * from "./StringValidator"; // exports interface StringValidator
export * from "./LettersOnlyValidator"; // exports class LettersOnlyValidator
export * from "./ZipCodeValidator";  // exports class ZipCodeValidator
```

# <a name="import"></a>匯入

模組的匯入操作與匯出一樣簡單。
可以使用以下`import`形式之一來匯入其它模組中的匯出內容。

## 匯入一個模組中的某個匯出內容

```ts
import { ZipCodeValidator } from "./ZipCodeValidator";

let myValidator = new ZipCodeValidator();
```

可以對匯入內容重新命名

```ts
import { ZipCodeValidator as ZCV } from "./ZipCodeValidator";
let myValidator = new ZCV();
```

## 將整個模組匯入到一個變數，並通過它來訪問模組的匯出部分

```ts
import * as validator from "./ZipCodeValidator";
let myValidator = new validator.ZipCodeValidator();
```

## 具有副作用的匯入模組

儘管不推薦這麼做，一些模組會設定一些全局狀態供其它模組使用。
這些模組可能沒有任何的匯出或使用者根本就不關注它的匯出。
使用下面的方法來匯入這類模組：

```ts
import "./my-module.js";
```

# 預設匯出

每個模組都可以有一個`default`匯出。
預設匯出使用`default`關鍵字標記；並且一個模組只能夠有一個`default`匯出。
需要使用一種特殊的匯入形式來匯入`default`匯出。

`default`匯出十分便利。
比如，像JQuery這樣的類庫可能有一個預設匯出`jQuery`或`$`，並且我們基本上也會使用同樣的名字`jQuery`或`$`匯出JQuery。

##### JQuery.d.ts

```ts
declare let $: JQuery;
export default $;
```

##### App.ts

```ts
import $ from "JQuery";

$("button.continue").html( "Next Step..." );
```

類和函數聲明可以直接被標記為預設匯出。
標記為預設匯出的類和函數的名字是可以省略的。

##### ZipCodeValidator.ts

```ts
export default class ZipCodeValidator {
    static numberRegexp = /^[0-9]+$/;
    isAcceptable(s: string) {
        return s.length === 5 && ZipCodeValidator.numberRegexp.test(s);
    }
}
```

##### Test.ts

```ts
import validator from "./ZipCodeValidator";

let validator = new validator();
```

或者

##### StaticZipCodeValidator.ts

```ts
const numberRegexp = /^[0-9]+$/;

export default function (s: string) {
    return s.length === 5 && numberRegexp.test(s);
}
```

##### Test.ts

```ts
import validate from "./StaticZipCodeValidator";

let strings = ["Hello", "98052", "101"];

// Use function validate
strings.forEach(s => {
  console.log(`"${s}" ${validate(s) ? " matches" : " does not match"}`);
});
```

`default`匯出也可以是一個值

##### OneTwoThree.ts

```ts
export default "123";
```

##### Log.ts

```ts
import num from "./OneTwoThree";

console.log(num); // "123"
```

# `export =` 和 `import = require()`

CommonJS和AMD都有一個`exports`物件的概念，它包含了一個模組的所有匯出內容。

它們也支援把`exports`替換為一個自定義物件。
預設匯出就好比這樣一個功能；然而，它們卻並不相互相容。
TypeScript模組支援`export =`語法，以配合傳統的CommonJS和AMD的工作流。

`export =`語法定義一個模組的匯出物件。
它可以是類，介面，名稱空間，函數或列舉。

若要匯入一個使用了`export =`的模組時，必須使用TypeScript提供的特定語法`import let = require("module")`。

##### ZipCodeValidator.ts

```ts
let numberRegexp = /^[0-9]+$/;
class ZipCodeValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
```

##### Test.ts

```ts
import zip = require("./ZipCodeValidator");

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validator = new zip.ZipCodeValidator();

// Show whether each string passed each validator
strings.forEach(s => {
  console.log(`"${ s }" - ${ validator.isAcceptable(s) ? "matches" : "does not match" }`);
});
```


# 生成模組程式碼

根據編譯時指定的模組目標參數，編譯器會生成相應的供Node.js ([CommonJS](http://wiki.commonjs.org/wiki/CommonJS))，Require.js ([AMD](https://github.com/amdjs/amdjs-api/wiki/AMD))，isomorphic ([UMD](https://github.com/umdjs/umd)), [SystemJS](https://github.com/systemjs/systemjs)或[ECMAScript 2015 native modules](http://www.ecma-international.org/ecma-262/6.0/#sec-modules) (ES6)模組載入系統使用的程式碼。
想要了解生成程式碼中`define`，`require` 和 `register`的意義，請參考相應模組載入器的文件。

下面的例子說明了匯入匯出語句裡使用的名字是怎麼轉換為相應的模組載入器程式碼的。

##### SimpleModule.ts

```ts
import m = require("mod");
export let t = m.something + 1;
```

##### AMD / RequireJS SimpleModule.js

```js
define(["require", "exports", "./mod"], function (require, exports, mod_1) {
    exports.t = mod_1.something + 1;
});
```

##### CommonJS / Node SimpleModule.js

```js
let mod_1 = require("./mod");
exports.t = mod_1.something + 1;
```

##### UMD SimpleModule.js

```js
(function (factory) {
    if (typeof module === "object" && typeof module.exports === "object") {
        let v = factory(require, exports); if (v !== undefined) module.exports = v;
    }
    else if (typeof define === "function" && define.amd) {
        define(["require", "exports", "./mod"], factory);
    }
})(function (require, exports) {
    let mod_1 = require("./mod");
    exports.t = mod_1.something + 1;
});
```

##### System SimpleModule.js

```js
System.register(["./mod"], function(exports_1) {
    let mod_1;
    let t;
    return {
        setters:[
            function (mod_1_1) {
                mod_1 = mod_1_1;
            }],
        execute: function() {
            exports_1("t", t = mod_1.something + 1);
        }
    }
});
```

##### Native ECMAScript 2015 modules SimpleModule.js

```js
import { something } from "./mod";
export let t = something + 1;
```

# 簡單示例

下面我們來整理一下前面的驗證器實現，每個模組只有一個命名的匯出。

為了編譯，我們必需要在命令列上指定一個模組目標。對於Node.js來說，使用`--module commonjs`；
對於Require.js來說，使用``--module amd`。比如：

```Shell
tsc --module commonjs Test.ts
```

編譯完成後，每個模組會生成一個單獨的`.js`檔案。
好比使用了reference標籤，編譯器會根據`import`語句編譯相應的檔案。


##### Validation.ts

```ts
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

##### LettersOnlyValidator.ts

```ts
import { StringValidator } from "./Validation";

const lettersRegexp = /^[A-Za-z]+$/;

export class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
```

##### ZipCodeValidator.ts

```ts
import { StringValidator } from "./Validation";

const numberRegexp = /^[0-9]+$/;

export class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
```

##### Test.ts

```ts
import { StringValidator } from "./Validation";
import { ZipCodeValidator } from "./ZipCodeValidator";
import { LettersOnlyValidator } from "./LettersOnlyValidator";

// Some samples to try
let strings = ["Hello", "98052", "101"];

// Validators to use
let validators: { [s: string]: StringValidator; } = {};
validators["ZIP code"] = new ZipCodeValidator();
validators["Letters only"] = new LettersOnlyValidator();

// Show whether each string passed each validator
strings.forEach(s => {
    for (let name in validators) {
        console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
    }
});
```

# 可選的模組載入和其它高階載入場景

有時候，你只想在某種條件下才載入某個模組。
在TypeScript裡，使用下面的方式來實現它和其它的高階載入場景，我們可以直接呼叫模組載入器並且可以保證類型完全。

編譯器會檢測是否每個模組都會在生成的JavaScript中用到。
如果一個模組標識符只在類型註解部分使用，並且完全沒有在表示式中使用時，就不會生成`require`這個模組的程式碼。
省略掉沒有用到的引用對效能提升是很有益的，並同時提供了選擇性載入模組的能力。

這種模式的核心是`import id = require("...")`語句可以讓我們訪問模組匯出的類型。
模組載入器會被動態呼叫（通過`require`），就像下面`if`程式碼塊裡那樣。
它利用了省略引用的優化，所以模組只在被需要時載入。
為了讓這個模組工作，一定要注意`import`定義的標識符只能在表示類型處使用（不能在會轉換成JavaScript的地方）。

為了確保類型安全性，我們可以使用`typeof`關鍵字。
`typeof`關鍵字，當在表示類型的地方使用時，會得出一個類型值，這裡就表示模組的類型。

##### 示例：Node.js裡的動態模組載入

```ts
declare function require(moduleName: string): any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
    let ZipCodeValidator: typeof Zip = require("./ZipCodeValidator");
    let validator = new ZipCodeValidator();
    if (validator.isAcceptable("...")) { /* ... */ }
}
```

##### 示例：require.js裡的動態模組載入

```ts
declare function require(moduleNames: string[], onLoad: (...args: any[]) => void): void;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
    require(["./ZipCodeValidator"], (ZipCodeValidator: typeof Zip) => {
        let validator = new ZipCodeValidator();
        if (validator.isAcceptable("...")) { /* ... */ }
    });
}
```

##### 示例：System.js裡的動態模組載入

```ts
declare let System: any;

import { ZipCodeValidator as Zip } from "./ZipCodeValidator";

if (needZipValidation) {
    System.import("./ZipCodeValidator").then((ZipCodeValidator: typeof Zip) => {
        let x = new ZipCodeValidator();
        if (x.isAcceptable("...")) { /* ... */ }
    });
}
```

# 使用其它的JavaScript庫

為了描述不是用TypeScript編寫的類庫的類型，我們需要聲明類庫匯出的API。

我們叫它聲明因為它不是外部程式的具體實現。
通常會在`.d.ts`裡寫這些定義。
如果你熟悉C/C++，你可以把它們當做`.h`檔案。
讓我們看一些例子。

## 外部模組

在Node.js裡大部分工作是通過載入一個或多個模組實現的。
我們可以使用頂級的`export`聲明來為每個模組都定義一個`.d.ts`檔案，但最好還是寫在一個大的`.d.ts`檔案裡。
我們使用與構造一個外部名稱空間相似的方法，但是這裡使用`module`關鍵字並且把名字用引號括起來，方便之後`import`。
例如：

##### node.d.ts (simplified excerpt)

```ts
declare module "url" {
    export interface Url {
        protocol?: string;
        hostname?: string;
        pathname?: string;
    }

    export function parse(urlStr: string, parseQueryString?, slashesDenoteHost?): Url;
}

declare module "path" {
    export function normalize(p: string): string;
    export function join(...paths: any[]): string;
    export let sep: string;
}
```

現在我們可以`/// <reference>` `node.d.ts`並且使用`import url = require("url");`載入模組。

```ts
/// <reference path="node.d.ts"/>
import * as URL from "url";
let myUrl = URL.parse("http://www.typescriptlang.org");
```

# 建立模組結構指導

## 儘可能地在頂層匯出

使用者應該更容易地使用你模組匯出的內容。
巢狀層次過多會變得難以處理，因此仔細考慮一下如何組織你的程式碼。

從你的模組中匯出一個名稱空間就是一個增加巢狀的例子。
雖然名稱空間有時候有它們的用處，在使用模組的時候它們額外地增加了一層。
這對使用者來說是很不便的並且通常是多餘的。

匯出類的靜態方法也有同樣的問題 - 這個類本身就增加了一層巢狀。
除非它能方便表述或便於清晰使用，否則請考慮直接匯出一個輔助方法。

### 如果僅匯出單個 `class` 或 `function`，使用 `export default`

就像“在頂層上匯出”幫助減少使用者使用的難度，一個預設的匯出也能起到這個效果。
如果一個模組就是為了匯出特定的內容，那麼你應該考慮使用一個預設匯出。
這會令模組的匯入和使用變得些許簡單。
比如：

#### MyClass.ts

```ts
export default class SomeType {
  constructor() { ... }
}
```

#### MyFunc.ts

```ts
export default function getThing() { return 'thing'; }
```

#### Consumer.ts

```ts
import t from "./MyClass";
import f from "./MyFunc";
let x = new t();
console.log(f());
```

對使用者來說這是最理想的。他們可以隨意命名匯入模組的類型（本例為`t`）並且不需要多餘的（.）來找到相關物件。

### 如果要匯出多個物件，把它們放在頂層裡匯出

#### MyThings.ts

```ts
export class SomeType { /* ... */ }
export function someFunc() { /* ... */ }
```

相反地，當匯入的時候：

### 明確地列出匯入的名字

#### Consumer.ts

```ts
import { SomeType, SomeFunc } from "./MyThings";
let x = new SomeType();
let y = someFunc();
```

### 使用名稱空間匯入模式當你要匯出大量內容的時候

#### MyLargeModule.ts

```ts
export class Dog { ... }
export class Cat { ... }
export class Tree { ... }
export class Flower { ... }
```

#### Consumer.ts

```ts
import * as myLargeModule from "./MyLargeModule.ts";
let x = new myLargeModule.Dog();
```

## 使用重新匯出進行擴充套件

你可能經常需要去擴充套件一個模組的功能。
JS裡常用的一個模式是JQuery那樣去擴充套件原物件。
如我們之前提到的，模組不會像全局名稱空間物件那樣去*合併*。
推薦的方案是*不要*去改變原來的物件，而是匯出一個新的實體來提供新的功能。

假設`Calculator.ts`模組裡定義了一個簡單的計算器實現。
這個模組同樣提供了一個輔助函數來測試計算器的功能，通過傳入一系列輸入的字元串並在最後給出結果。

#### Calculator.ts

```ts
export class Calculator {
    private current = 0;
    private memory = 0;
    private operator: string;

    protected processDigit(digit: string, currentValue: number) {
        if (digit >= "0" && digit <= "9") {
            return currentValue * 10 + (digit.charCodeAt(0) - "0".charCodeAt(0));
        }
    }

    protected processOperator(operator: string) {
        if (["+", "-", "*", "/"].indexOf(operator) >= 0) {
            return operator;
        }
    }

    protected evaluateOperator(operator: string, left: number, right: number): number {
        switch (this.operator) {
            case "+": return left + right;
            case "-": return left - right;
            case "*": return left * right;
            case "/": return left / right;
        }
    }

    private evaluate() {
        if (this.operator) {
            this.memory = this.evaluateOperator(this.operator, this.memory, this.current);
        }
        else {
            this.memory = this.current;
        }
        this.current = 0;
    }

    public handelChar(char: string) {
        if (char === "=") {
            this.evaluate();
            return;
        }
        else {
            let value = this.processDigit(char, this.current);
            if (value !== undefined) {
                this.current = value;
                return;
            }
            else {
                let value = this.processOperator(char);
                if (value !== undefined) {
                    this.evaluate();
                    this.operator = value;
                    return;
                }
            }
        }
        throw new Error(`Unsupported input: '${char}'`);
    }

    public getResult() {
        return this.memory;
    }
}

export function test(c: Calculator, input: string) {
    for (let i = 0; i < input.length; i++) {
        c.handelChar(input[i]);
    }

    console.log(`result of '${input}' is '${c.getResult()}'`);
}
```

這是使用匯出的`test`函數來測試計算器。

#### TestCalculator.ts

```ts
import { Calculator, test } from "./Calculator";


let c = new Calculator();
test(c, "1+2*33/11="); // prints 9
```

現在擴充套件它，新增支援輸入其它進位制（十進位制以外），讓我們來建立`ProgrammerCalculator.ts`。

#### ProgrammerCalculator.ts

```ts
import { Calculator } from "./Calculator";

class ProgrammerCalculator extends Calculator {
    static digits = ["0", "1", "2", "3", "4", "5", "6", "7", "8", "9", "A", "B", "C", "D", "E", "F"];

    constructor(public base: number) {
        super();
        if (base <= 0 || base > ProgrammerCalculator.digits.length) {
            throw new Error("base has to be within 0 to 16 inclusive.");
        }
    }

    protected processDigit(digit: string, currentValue: number) {
        if (ProgrammerCalculator.digits.indexOf(digit) >= 0) {
            return currentValue * this.base + ProgrammerCalculator.digits.indexOf(digit);
        }
    }
}

// Export the new extended calculator as Calculator
export { ProgrammerCalculator as Calculator };

// Also, export the helper function
export { test } from "./Calculator";
```

新的`ProgrammerCalculator`模組匯出的API與原先的`Calculator`模組很相似，但卻沒有改變原模組裡的物件。
下面是測試ProgrammerCalculator類的程式碼：


#### TestProgrammerCalculator.ts

```ts
import { Calculator, test } from "./ProgrammerCalculator";

let c = new Calculator(2);
test(c, "001+010="); // prints 3
```

## 模組裡不要使用名稱空間

當初次進入基於模組的開發模式時，可能總會控制不住要將匯出包裹在一個名稱空間裡。
模組具有其自己的作用域，並且只有匯出的聲明才會在模組外部可見。
記住這點，名稱空間在使用模組時幾乎沒什麼價值。

在組織方面，名稱空間對於在全局作用域內對邏輯上相關的物件和類型進行分組是很便利的。
例如，在C#裡，你會從`System.Collections`裡找到所有集合的類型。
通過將類型有層次地組織在名稱空間裡，可以方便使用者找到與使用那些類型。
然而，模組本身已經存在於檔案系統之中，這是必須的。
我們必須通過路徑和檔名找到它們，這已經提供了一種邏輯上的組織形式。
我們可以建立`/collections/generic/`資料夾，把相應模組放在這裡面。

名稱空間對解決全局作用域裡命名衝突來說是很重要的。
比如，你可以有一個`My.Application.Customer.AddForm`和`My.Application.Order.AddForm` -- 兩個類型的名字相同，但名稱空間不同。
然而，這對於模組來說卻不是一個問題。
在一個模組裡，沒有理由兩個物件擁有同一個名字。
從模組的使用角度來說，使用者會挑出他們用來引用模組的名字，所以也沒有理由發生重名的情況。

> 更多關於模組和名稱空間的資料檢視[名稱空間和模組](./Namespaces and Modules.md)

## 危險訊號

以下均為模組結構上的危險訊號。重新檢查以確保你沒有在對模組使用名稱空間：

* 檔案的頂層聲明是`export namespace Foo { ... }` （刪除`Foo`並把所有內容向上層移動一層）
* 檔案只有一個`export class`或`export function` （考慮使用`export default`）
* 多個檔案的頂層具有同樣的`export namespace Foo {` （不要以為這些會合併到一個`Foo`中！）