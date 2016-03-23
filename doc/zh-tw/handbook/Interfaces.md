# 介紹

TypeScript的核心原則之一是對值所具有的*shape*進行類型檢查。
它有時被稱做“鴨式辨型法”或“結構性子類型化”。
在TypeScript裡，介面的作用就是為這些類型命名和為你的程式碼或第三方程式碼定義契約。

# 介面初探

下面通過一個簡單示例來觀察介面是如何工作的：

```ts
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label);
}

let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```

類型檢查器會檢視`printLabel`的呼叫。
`printLabel`有一個參數，並要求這個物件參數有一個名為`label`類型為`string`的屬性。
需要注意的是，我們傳入的物件參數實際上會包含很多屬性，但是編譯器只會檢查那些必需的屬性是否存在，並且其類型是否匹配。
然而，有些時候TypeScript卻並不會這麼寬鬆，我們下在會稍做講解。

下面我們重寫上面的例子，這次使用介面來描述：必須包含一個`label`屬性且類型為`string`：

```ts
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

let myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

`LabelledValue`介面就好比一個名字，用來描述上面例子裡的要求。
它代表了有一個`label`屬性且類型為`string`的物件。
需要注意的是，我們在這裡並不能像在其它語言裡一樣，說傳給`printLabel`的物件實現了這個介面。我們只會去關注值的外形。
只要傳入的物件滿足上面提到的必要條件，那麼它就是被允許的。

還有一點值得提的是，類型檢查器不會去檢查屬性的順序，只要相應的屬性存在並且類型也是對的就可以。

# 可選屬性

接口裡的屬性不全都是必需的。
有些是隻在某些條件下存在，或者根本不存在。
可選屬性在應用“option bags”模式時很常用，即給函數傳入的參數物件中只有部分屬性賦值了。

下面是應用了“option bags”的例子：

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});
```

帶有可選屬性的介面與普通的介面定義差不多，只是在可選屬性名字定義的後面加一個`?`符號。

可選屬性的好處之一是可以對可能存在的屬性進行預定義，好處之二是可以捕獲引用了不存在的屬性時的錯誤。
比如，我們故意將`createSquare`裡的`color`屬性名拼錯，就會得到一個錯誤提示：

```ts
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
  let newSquare = {color: "white", area: 100};
  if (config.color) {
    // Error: Property 'collor' does not exist on type 'SquareConfig'
    newSquare.color = config.collor;  // Type-checker can catch the mistyped name here
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

let mySquare = createSquare({color: "black"});
```

# 額外的屬性檢查

我們在第一個例子裡使用了介面，TypeScript讓我們傳入`{ size: number; label: string; }`到僅期望得到`{ label: string; }`的函數裡。
我們已經學過了可選屬性，並且知道他們在“option bags”模式裡很有用。

然而，天真地將這兩者結合的話就會像在JavaScript裡那樣搬起石頭砸自己的腳。
比如，拿`createSquare`例子來說：

```ts
interface SquareConfig {
    color?: string;
    width?: number;
}

function createSquare(config: SquareConfig): { color: string; area: number } {
    // ...
}

let mySquare = createSquare({ colour: "red", width: 100 });
```

注意傳入`createSquare`的參數拼寫為*`colour`*而不是`color`。
在JavaScript裡，這會默默地失敗。

你可以會爭辯這個程式已經正確的類型化了，因為`width`屬性是相容的，不存在`color`屬性，而且額外的`colour`屬性是無意義的。

然而，TypeScript會認為這段程式碼可能存在bug。
物件字面量會被特殊對待而且會經過*額外屬性檢查*，當將它們賦值給變數或作為參數傳遞的時候。
如果一個物件字面量存在任何“目標類型”不包含的屬性時，你會得到一個錯誤。

```ts
// error: 'colour' not expected in type 'SquareConfig'
let mySquare = createSquare({ colour: "red", width: 100 });
```

繞開這些檢查非常簡單。
最好而簡便的方法是使用類型斷言：

```ts
let mySquare = createSquare({ colour: "red", width: 100 } as SquareConfig);
```

另一個方法，可能會讓人有點驚壓，就是將一個物件賦值給別一個變數：

```ts
let squareOptions = { colour: "red", width: 100 };
let mySquare = createSquare(squareOptions);
```

因為`squareOptions`不會經過額外屬性檢查，所以編譯器不會報錯。

要留意，在像上面一樣的簡單程式碼裡，你可能不應該去繞開這些檢查。
對於包含方法和內部狀態的複雜物件字面量來講，你可能需要使用這些技巧，但是大部額外屬性檢查錯誤是真正的bug。
就是說你遇到了額外類型檢查出的錯誤，比如選擇包，你應該去審查一下你的類型聲明。
在這裡，如果支援傳入`color`或`colour`屬性到`createSquare`，你應該修改`SquareConfig`定義來體現出這一點。

# 函數類型

介面能夠描述JavaScript中物件擁有的各種各樣的外形。
除了描述帶有屬性的普通物件外，介面也可以描述函數類型。

為了使用介面表示函數類型，我們需要給介面定義一個呼叫簽名。
它就像是一個只有參數列表和返回值類型的函數定義。參數列表裡的每個參數都需要名字和類型。

```ts
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

這樣定義後，我們可以像使用其它介面一樣使用這個函數類型的介面。
下例展示瞭如何建立一個函數類型的變數，並將一個同類型的函數賦值給這個變數。

```ts
let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  let result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

對於函數類型的類型檢查來說，函數的參數名不需要與接口裡定義的名字相匹配。
比如，我們使用下面的程式碼重寫上面的例子：

```ts
let mySearch: SearchFunc;
mySearch = function(src: string, sub: string): boolean {
  let result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

函數的參數會逐個進行檢查，要求對應位置上的參數類型是相容的。
如果你不想指定類型，Typescript的類型系統會推斷出參數類型，因為函數直接賦值給了`SearchFunc`類型變數。
函數的返回值類型是通過其返回值推斷出來的（此例是`false`和`true`）。
如果讓這個函數返回數字或字元串，類型檢查器會警告我們函數的返回值類型與`SearchFunc`介面中的定義不匹配。

```ts
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    if (result == -1) {
        return false;
    }
    else {
        return true;
    }
}
```

# 陣列類型

與使用介面描述函數類型差不多，我們也可以描述陣列類型。
陣列類型具有一個`index`類型表示索引的類型，還有一個相應的返回值類型表示通過索引得到的元素的類型。

```ts
interface StringArray {
  [index: number]: string;
}

let myArray: StringArray;
myArray = ["Bob", "Fred"];
```

支援兩種索引類型：string和number。
陣列可以同時使用這兩種索引類型，但是有一個限制，數字索引返回值的類型必須是字元串索引返回值的類型的子類型。

索引簽名能夠很好的描述陣列和`dictionary`模式，它們也要求所有屬性要與返回值類型相匹配。
因為字元串索引表明`obj.property`和`obj["property"]`兩種形式都可以。
下面的例子裡，`name`的類型與字元串索引類型不匹配，所以類型檢查器給出一個錯誤提示：

```ts
interface NumberDictionary {
  [index: string]: number;
  length: number;    // 可以，length是number類型
  name: string       // 錯誤，`name`的類型不是索引類型的子類型
}
```

# 類類型

## 實現介面

與C#或Java裡介面的基本作用一樣，TypeScript也能夠用它來明確的強制一個類去符合某種契約。

```ts
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

你也可以在介面中描述一個方法，在類裡實現它，如同下面的`setTime`方法一樣：

```ts
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

介面描述了類的公共部分，而不是公共和私有兩部分。
它不會幫你檢查類是否具有某些私有成員。

## 類靜態部分與例項部分的區別

當你操作類和介面的時候，你要知道類是具有兩個類型的：靜態部分的類型和例項的類型。
你會注意到，當你用構造器簽名去定義一個介面並試圖定義一個類去實現這個介面時會得到一個錯誤：

```ts
interface ClockConstructor {
    new (hour: number, minute: number);
}

class Clock implements ClockConstructor {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

這裡因為當一個類實現了一個介面時，只對其例項部分進行類型檢查。
constructor存在於類的靜態部分，所以不在檢查的範圍內。

因此，我們應該直接操作類的靜態部分。
看下面的例子，我們定義了兩個介面，`ClockConstructor`為建構函式所用和`ClockInterface`為例項方法所用。
為了方便我們定義一個建構函式`createClock`，它用傳入的類型建立例項。

```ts
interface ClockConstructor {
    new (hour: number, minute: number): ClockInterface;
}
interface ClockInterface {
    tick();
}

function createClock(ctor: ClockConstructor, hour: number, minute: number): ClockInterface {
    return new ctor(hour, minute);
}

class DigitalClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("beep beep");
    }
}
class AnalogClock implements ClockInterface {
    constructor(h: number, m: number) { }
    tick() {
        console.log("tick tock");
    }
}

let digital = createClock(DigitalClock, 12, 17);
let analog = createClock(AnalogClock, 7, 32);
```

因為`createClock`的第一個參數是`ClockConstructor`類型，在`createClock(AnalogClock, 12, 17)`裡，會檢查`AnalogClock`是否符合建構函式簽名。

# 擴充套件介面

和類一樣，介面也可以相互擴充套件。
這讓我們能夠從一個接口裡複製成員到另一個接口裡，可以更靈活地將介面分割到可重用的模組裡。

```ts
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

一個介面可以繼承多個介面，創建出多個介面的合成介面。

```ts
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

let square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

# 混合類型

先前我們提過，介面能夠描述JavaScript裡豐富的類型。
因為JavaScript其動態靈活的特點，有時你會希望一個物件可以同時具有上面提到的多種類型。

一個例子就是，一個物件可以同時做為函數和物件使用，並帶有額外的屬性。

```ts
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

function getCounter(): Counter {
    let counter = <Counter>function (start: number) { };
    counter.interval = 123;
    counter.reset = function () { };
    return counter;
}

let c = getCounter();
c(10);
c.reset();
c.interval = 5.0;
```

在使用JavaScript第三方庫的時候，你可能需要像上面那樣去完整地定義類型。

# 介面繼承類

當介面繼承了一個類類型時，它會繼承類的成員但不包括其實現。
就好像介面聲明瞭所有類中存在的成員，但並沒有提供具體實現一樣。
介面同樣會繼承到類的private和protected成員。
這意味著當你建立了一個介面繼承了一個擁有私有或受保護的成員的類時，這個介面類型只能被這個類或其子類所實現（implement）。

這是很有用的，當你有一個很深層次的繼承，但是隻想你的程式碼只是針對擁有特定屬性的子類起作用的時候。子類除了繼承自基類外與基類沒有任何聯絡。
例：

```ts
class Control {
    private state: any;
}

interface SelectableControl extends Control {
    select(): void;
}

class Button extends Control {
    select() { }
}
class TextBox extends Control {
    select() { }
}
class Image extends Control {
}
class Location {
    select() { }
}
```

在上面的例子裡，`SelectableControl`包含了`Control`的所有成員，包括私有成員`state`。
因為`state`是私有成員，所以只能夠是`Control`的子類們才能實現`SelectableControl`介面。
因為只有`Control`的子類才能夠擁有一個聲明於`Control`的私有成員`state`，這對私有成員的相容性是必需的。

在`Control`類內部，是允許通過`SelectableControl`的例項來訪問私有成員`state`的。
實際上，`SelectableControl`就像`Control`一樣，並擁有一個`select`方法。
`Button`和`TextBox`類是`SelectableControl`的子類（因為它們都繼承自`Control`並有`select`方法），但`Image`和`Location`類並不是這樣的。
