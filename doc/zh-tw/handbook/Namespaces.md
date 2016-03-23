> **關於術語的一點說明:**
請務必注意一點，TypeScript 1.5裡術語名已經發生了變化。
“內部模組”現在稱做“名稱空間”。
“外部模組”現在則簡稱為“模組”，這是為了與[ECMAScript 2015](http://www.ecma-international.org/ecma-262/6.0/)裡的術語保持一致，(也就是說 `module X {` 相當於現在推薦的寫法 `namespace X {`)。

# 介紹

這篇文章描述瞭如何在TypeScript裡使用名稱空間（之前叫做“內部模組”）來組織你的程式碼。

就像我們在術語說明裡提到的那樣，“內部模組”現在叫做“名稱空間”。

另外，任何使用`module`關鍵字來聲明一個內部模組的地方都應該使用`namespace`關鍵字來替換。

這就避免了讓新的使用者被相似的名稱所迷惑。

# 第一步

我們先來寫一段程式並將在整篇文章中都使用這個例子。
我們定義幾個簡單的字元串驗證器，假設你會使用它們來驗證表單裡的使用者輸入或驗證外部資料。

## 所有的驗證器都放在一個檔案裡

```ts
interface StringValidator {
    isAcceptable(s: string): boolean;
}

let lettersRegexp = /^[A-Za-z]+$/;
let numberRegexp = /^[0-9]+$/;

class LettersOnlyValidator implements StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}

class ZipCodeValidator implements StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}

// Some samples to try
let strings = ["Hello", "98052", "101"];
// Validators to use
let validators: { [s: string]: StringValidator; } = {};
validators["ZIP code"] = new ZipCodeValidator();
validators["Letters only"] = new LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (let name in validators) {
        console.log(""" + s + "" " + (validators[name].isAcceptable(s) ? " matches " : " does not match ") + name);
    }
});
```

# 名稱空間

隨著更多驗證器的加入，我們需要一種手段來組織程式碼，以便於在記錄它們類型的同時還不用擔心與其它物件產生命名衝突。
因此，我們把驗證器包裹到一個名稱空間內，而不是把它們放在全局名稱空間下。

下面的例子裡，把所有與驗證器相關的類型都放到一個叫做`Validation`的名稱空間裡。
因為我們想讓這些介面和類在名稱空間之外也是可訪問的，所以需要使用`export`。
相反的，變數`lettersRegexp`和`numberRegexp`是實現的細節，不需要匯出，因此它們在名稱空間外是不能訪問的。
在檔案末尾的測試程式碼裡，由於是在名稱空間之外訪問，因此需要限定類型的名稱，比如`Validation.LettersOnlyValidator`。

## 使用名稱空間的驗證器

```ts
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    const lettersRegexp = /^[A-Za-z]+$/;
    const numberRegexp = /^[0-9]+$/;

    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }

    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}

// Some samples to try
let strings = ["Hello", "98052", "101"];
// Validators to use
let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (let name in validators) {
        console.log(`"${ s }" - ${ validators[name].isAcceptable(s) ? "matches" : "does not match" } ${ name }`);
    }
});
```

# 分離到多檔案

當應用變得越來越大時，我們需要將程式碼分離到不同的檔案中以便於維護。

## 多檔案中的名稱空間

現在，我們把`Validation`名稱空間分割成多個檔案。
儘管是不同的檔案，它們仍是同一個名稱空間，並且在使用的時候就如同它們在一個檔案中定義的一樣。
因為不同檔案之間存在依賴關係，所以我們加入了引用標籤來告訴編譯器檔案之間的關聯。
我們的測試程式碼保持不變。

##### Validation.ts

```ts
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
```

##### LettersOnlyValidator.ts

```ts
/// <reference path="Validation.ts" />
namespace Validation {
    const lettersRegexp = /^[A-Za-z]+$/;
    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }
}
```

##### ZipCodeValidator.ts

```ts
/// <reference path="Validation.ts" />
namespace Validation {
    const numberRegexp = /^[0-9]+$/;
    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}
```

##### Test.ts

```ts
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

// Some samples to try
let strings = ["Hello", "98052", "101"];
// Validators to use
let validators: { [s: string]: Validation.StringValidator; } = {};
validators["ZIP code"] = new Validation.ZipCodeValidator();
validators["Letters only"] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (let name in validators) {
        console.log(""" + s + "" " + (validators[name].isAcceptable(s) ? " matches " : " does not match ") + name);
    }
});
```

當涉及到多檔案時，我們必須確保所有編譯後的程式碼都被載入了。
我們有兩種方式。

第一種方式，把所有的輸入檔案編譯為一個輸出檔案，需要使用`--outFile`標記：

```Shell
tsc --outFile sample.js Test.ts
```

編譯器會根據源碼裡的引用標籤自動地對輸出進行排序。你也可以單獨地指定每個檔案。

```Shell
tsc --outFile sample.js Validation.ts LettersOnlyValidator.ts ZipCodeValidator.ts Test.ts
```

第二種方式，我們可以編譯每一個檔案（預設方式），那麼每個原始檔都會對應生成一個JavaScript檔案。
然後，在頁面上通過`<script>`標籤把所有生成的JavaScript檔案按正確的順序引進來，比如：

##### MyTestPage.html (excerpt)

```ts
    <script src="Validation.js" type="text/javascript" />
    <script src="LettersOnlyValidator.js" type="text/javascript" />
    <script src="ZipCodeValidator.js" type="text/javascript" />
    <script src="Test.js" type="text/javascript" />
```

# 別名

另一種簡化名稱空間操作的方法是使用`import q = x.y.z`給常用的物件起一個短的名字。
不要與用來載入模組的`import x = require('name')`語法弄混了，這裡的語法是為指定的符號建立一個別名。
你可以用這種方法為任意標識符建立別名，也包括匯入的模組中的物件。

```ts
namespace Shapes {
    export namespace Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
let sq = new polygons.Square(); // Same as "new Shapes.Polygons.Square()"
```

注意，我們並沒有使用`require`關鍵字，而是直接使用匯入符號的限定名賦值。
這與使用`var`相似，但它還適用於類型和匯入的具有名稱空間含義的符號。
重要的是，對於值來講，`import`會生成與原始符號不同的引用，所以改變別名的`var`值並不會影響原始變數的值。


# 使用其它的JavaScript庫

為了描述不是用TypeScript編寫的類庫的類型，我們需要聲明類庫匯出的API。
由於大部分程式庫只提供少數的頂級物件，名稱空間是用來表示它們的一個好辦法。

我們稱其為聲明是因為它不是外部程式的具體實現。
我們通常在`.d.ts`裡寫這些聲明。
如果你熟悉C/C++，你可以把它們當做`.h`檔案。
讓我們看一些例子。

## 外部名稱空間

流行的程式庫D3在全局物件`d3`裡定義它的功能。
因為這個庫通過一個`<script>`標籤載入（不是通過模組載入器），它的聲明檔案使用內部模組來定義它的類型。
為了讓TypeScript編譯器識別它的類型，我們使用外部名稱空間聲明。
比如，我們可以像下面這樣寫：

##### D3.d.ts (部分摘錄)

```ts
declare namespace D3 {
    export interface Selectors {
        select: {
            (selector: string): Selection;
            (element: EventTarget): Selection;
        };
    }

    export interface Event {
        x: number;
        y: number;
    }

    export interface Base extends Selectors {
        event: Event;
    }
}

declare let d3: D3.Base;
```
