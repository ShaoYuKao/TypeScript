# 聯合類型

偶爾你會遇到這種情況，一個第三方庫可以傳入或返回`number`或`string`類型的資料。
例如下面的函數：

```ts
/**
 * 拿到一個字元串並在左邊新增"padding"。
 * 如果 'padding' 是個字元串, 那麼 'padding' 被加到左邊。
 * 如果 'padding' 是個數字, 那麼在左邊加入此數量的空格.
 */
function padLeft(value: string, padding: any) {
    // ...
}

padLeft("Hello world", 4); // 返回 "    Hello world"
```

`padLeft`存在一個問題，`padding`參數的類型指定成了`any`。
這就是說我們可以傳入一個既不是`number`也不是`string`類型的參數，但是TypeScript卻不報錯。

```ts
let indentedString = padLeft("Hello world", true); // 編譯階段通過。執行時報錯
```

在一些傳統的面嚮物件語言裡，我們可能將這兩種類型抽象成有層級的類型。

```ts
interface Padder {
    getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces).join(" ");
    }
}

class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}

function padLeft(value: string, padder: Padder) {
    return padder.getPaddingString() + value;
}

padLeft("Hello world", new SpaceRepeatingPadder(4));
```

這樣就清楚多了，但是做的也有點過。
原始版本的`padLeft`有一點好處是我們可以傳入一個原始值。
使用起來清晰明瞭。
如果我們想定義一個已經存在了的函數那麼新的方案也不合適了。

不用`any`，我們可以使用*聯合類型*做為`padding`的參數：

```ts
/**
 * Takes a string and adds "padding" to the left.
 * If 'padding' is a string, then 'padding' is appended to the left side.
 * If 'padding' is a number, then that number of spaces is added to the left side.
 */
function padLeft(value: string, padding: string | number) {
    // ...
}

let indentedString = padLeft("Hello world", true); // errors during compilation
```

聯合類型表示一個值可以是幾種類型之一。
我們用豎線（`|`）分隔每個類型，所以`number | string | boolean`表示一個值可以是`number`，`string`，或`boolean`。

如果一個值是聯合類型，我們只能訪問此聯合類型的所有類型裡共有的成員。

```ts
interface Bird {
    fly();
    layEggs();
}

interface Fish {
    swim();
    layEggs();
}

function getSmallPet(): Fish | Bird {
    // ...
}

let pet = getSmallPet();
pet.layEggs(); // okay
pet.swim();    // errors
```

這裡的聯合類型可能有點複雜，但是你很容易就習慣了。
如果一個值類型是`A | B`，我們只能*確定*它具有成員同時存在於`A`*和*`B`裡。
這個例子裡，`Bird`具有一個`fly`成員。
我們不能確定一個`Bird | Fish`類型的變數是否有`fly`方法。
如果變數在執行時是`Fish`類型，那麼呼叫`pet.fly()`就出錯了。

# 類型保護與區分類型

聯合類型非常適合這樣的情形，可接收的值有不同的類型。
當我們想明確地知道是否拿到`Fish`時會怎麼做？
JavaScript裡常用來區分2個可能值的方法是檢查它們是否存在。
像之前提到的，我們只能訪問聯合類型的所有類型中共有的成員。

```ts
let pet = getSmallPet();

// 每一個成員訪問都會報錯
if (pet.swim) {
    pet.swim();
}
else if (pet.fly) {
    pet.fly();
}
```

為了讓這碼程式碼工作，我們要使用類型斷言：

```ts
let pet = getSmallPet();

if ((<Fish>pet).swim) {
    (<Fish>pet).swim();
}
else {
    (<Bird>pet).fly();
}
```

## 使用者自定義的類型保護

可以注意到我們使用了多次類型斷言。
如果我們只要檢查過一次類型，就能夠在後面的每個分支裡清楚`pet`的類型的話就好了。

TypeScript裡的*類型保護*機制讓它成為了現實。
類型保護就是一些表示式，它們會在執行時檢查以確保在某個作用域裡的類型。
要定義一個類型保護，我們只要簡單地定義一個函數，它的返回值是一個*類型斷言*：

```ts
function isFish(pet: Fish | Bird): pet is Fish {
    return (<Fish>pet).swim !== undefined;
}
```

在這個例子裡，`pet is Fish`就是類型斷言。
一個斷言是`parameterName is Type`這種形式，`parameterName`必須是來自於當前函數簽名裡的一個參數名。

每當使用一些變數呼叫`isFish`時，TypeScript會將變數縮減為那個具體的類型，只要這個類型與變數的原始類型是相容的。

```ts
// 'swim' 和 'fly' 呼叫都沒有問題了

if (isFish(pet) {
    pet.swim();
}
else {
    pet.fly();
}
```

注意TypeScript不僅知道在`if`分支裡`pet`是`Fish`類型；
它還清楚在`else`分支裡，一定*不是*`Fish`類型，一定是`Bird`類型。

## `typeof`類型保護

我們還沒有真正的展示過如何使用聯合類型來實現`padLeft`。
我們可以像下面這樣利用類型斷言來寫：

```ts
function isNumber(x: any): x is number {
    return typeof x === "number";
}

function isString(x: any): x is string {
    return typeof x === "string";
}

function padLeft(value: string, padding: string | number) {
    if (isNumber(padding)) {
        return Array(padding).join(" ") + value;
    }
    if (isString(padding)) {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${value}'.`);
}
```

然而，必須要定義一個函數來判斷類型是否是原始類型，這太痛苦了。
幸運的是，現在我們不必將`typeof x === "number"`抽象成一個函數，因為TypeScript可以將它識別為一個類型保護。
也就是說我們可以直接在程式碼裡檢查類型了。

```ts
function padLeft(value: string, padding: string | number) {
    if (typeof padding === "number") {
        return Array(padding).join(" ") + value;
    }
    if (typeof padding === "string") {
        return padding + value;
    }
    throw new Error(`Expected string or number, got '${value}'.`);
}
```

這些*`typeof`類型保護*只有2個形式能被識別：`typeof v === "typename"`和`typeof v !== "typename"`，`"typename"`必須是`"number"`，`"string"`，`"boolean"`或`"symbol"`。
但是TypeScript並不會阻止你使用除這些以外的字元串，或者將它們位置對換，且語言不會把它們識別為類型保護。

## `instanceof`類型保護

如果你已經閱讀了`typeof`類型保護並且對JavaScript裡的`instanceof`操作符熟悉的話，你可能已經猜到了這節要講的內容。

*`instanceof`類型保護*是通過其建構函式來細化其類型。
比如，我們借鑑一下之前字元串填充的例子：

```ts
interface Padder {
    getPaddingString(): string
}

class SpaceRepeatingPadder implements Padder {
    constructor(private numSpaces: number) { }
    getPaddingString() {
        return Array(this.numSpaces).join(" ");
    }
}

class StringPadder implements Padder {
    constructor(private value: string) { }
    getPaddingString() {
        return this.value;
    }
}

function getRandomPadder() {
    return Math.random() < 0.5 ?
        new SpaceRepeatingPadder(4) :
        new StringPadder("  ");
}

// 類型為SpaceRepeatingPadder | StringPadder
let padder: Padding = getRandomPadder();

if (padder instanceof SpaceRepeatingPadder) {
    padder; // 類型細化為'SpaceRepeatingPadder'
}
if (padder instanceof StringPadder) {
    padder; // 類型細化為'StringPadder'
}
```

`instanceof`的右側要求為一個建構函式，TypeScript將細化為：

1. 這個函數的`prototype`屬性，如果它的類型不為`any`的話
2. 類型中構造簽名所返回的類型的聯合，順序保持一至。

# 類型別名

類型別名會給一個類型起個新名字。
類型別名有時和介面很像，但是可以作用於原始值，聯合類型，元組以及其它任何你需要手寫的類型。

```ts
type XCoord = number;
type YCoord = number;

type XYCoord = { x: XCoord; y: YCoord };
type XYZCoord = { x: XCoord; y: YCoord; z: number };

type Coordinate = XCoord | XYCoord | XYZCoord;
type CoordList = Coordinate[];

let coord: CoordList = [{ x: 10, y: 10}, { x: 0, y: 42, z: 10 }, 5];
```

起別名不會新建一個類型 - 它建立了一個新*名字*來引用那個類型。
所以`10`是絕對有效的`XCoord`和`YCoord`，因為它們都引用`number`。
給原始類型起別名通常沒什麼用，儘管可以做為文件的一種形式使用。

同介面一樣，類型別名也可以是泛型 - 我們可以新增類型參數並且在別名聲明的右側傳入：

```ts
type Container<T> = { value: T };
```

我們也可以使用類型別名來在屬性裡引用自己：

```ts
type Tree<T> = {
    value: T;
    left: Tree<T>;
    right: Tree<T>;
}
```

然而，類型別名不可能出現在聲明右側以外的地方：

```ts
type Yikes = Array<Yikes>; // 錯誤
```

## 介面 vs. 類型別名

像我們提到的，類型別名可以像介面一樣；然而，仍有一些細微差別。

一個重要區別是類型別名不能被`extends`和`implements`也不能去`extends`和`implements`其它類型。
因為[軟體中的物件應該對於擴充套件是開放的，但是對於修改是封閉的](https://en.wikipedia.org/wiki/Open/closed_principle)，你應該儘量去使用介面代替類型別名。

另一方面，如果你無法通過介面來描述一個類型並且需要使用聯合類型或元組類型，這時通常會使用類型別名。
