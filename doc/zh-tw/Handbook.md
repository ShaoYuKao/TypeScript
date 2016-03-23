# TypeScript教學手冊

正體翻譯：陳傳興 ([@kkbruce](https://github.com/kkbruce))

2016年3月

TypeScript是微軟公司的注冊商標。

## 目錄

* [基本型別](#1)
  * [Boolean](#1.1)
  * [Number](#1.2)
  * [String](#1.3)
  * [Array](#1.4)
  * [Enum](#1.5)
  * [Any](#1.6)
  * [Void](#1.7)
* [介面](#2)
  * [介面初探](#2.1)
  * [選擇性屬性](#2.2)
  * [函數型別](#2.3)
  * [陣列型別](#2.4)
  * [類別型別](#2.5)
  * [擴充介面](#2.6)
  * [混合型別](#2.7)
* [類別](#3)
  * [類別](#3.1)
  * [繼承](#3.2)
  * [private/public等修飾符](#3.3)
  * [存取器](#3.4)
  * [靜態屬性](#3.5)
  * [進階技巧](#3.6)
* [命名空間和模組](#4)
  * [第一步](#第一步)
  * [使用命名空間](#使用命名空間)
  * [分割成多檔](#分割成多檔)
  * [模組](#4.2)
  * [Export =](#4.3)
  * [別名](#4.4)
  * [選擇性模組的載入與其他進階載入的場景](#4.5)
  * [使用其它JavaScript函式庫](#4.6)
  * [模組的陷阱](#4.7)
* [函數](#5)
  * [函數](#5.1)
  * [函數型別](#5.2)
  * [選擇性參數和預設參數](#5.3)
  * [其餘參數](#5.4)
  * [Lambda運算式和使用‘this’](#5.5)
  * [多載](#5.6)
* [泛型](#6)
  * [Hello World的泛型](#6.1)
  * [使用泛型變數](#6.2)
  * [泛型型別](#6.3)
  * [泛型類別](#6.4)
  * [泛型約束](#6.5)
* [常見錯誤](#7)
  * [常見疑難問題](#7.1)
* [Mixins](#8)
  * [Mixin 例子](#8.1)
  * [理解這個例子](#8.2)
* [宣告式合併](#9)
  * [基礎概念](#9.1)
  * [合併介面](#9.2)
  * [合併模組](#9.3)
  * [模組、類別、函數與列舉的合併](#9.4)
  * [無效的合併](#9.5)
* [型別推論](#10)
  * [基礎](#10.1)
  * [最佳通用型別](#10.2)
  * [相關聯型別](#10.3)
* [型別相容性](#11)
  * [開始](#11.1)
  * [比較函數](#11.2)
  * [列舉型別](#11.3)
  * [類別](#11.4)
  * [泛型](#11.5)
  * [進階主題](#11.6)
* [書寫.d.ts文件](#12)
  * [指導與說明](#12.1)
  * [範例](#12.2)

## <a name="1"></a>基本型別

為了讓程式有價值，我們需要能夠處理最簡單的資料單元：數值、字串、結構，布林值等。TypeScript支持與JavaScript幾乎相同的資料型別，此外還提供了實用的舉列型別方便我們使用。

### <a name="1.1"></a>Boolean

最基本的資料型別就是簡單的true/false值，在JavaScript和TypeScript裡叫做`boolean`（其它語言中也一樣）。

```typescript
var isDone: boolean = false;
```

### <a name="1.2"></a>Number

和JavaScript一樣，TypeScript裡的所有數值都是浮點數。這些浮點數的型別是`number`。

```typescript
var height: number = 6;
```

### <a name="1.3"></a>String

JavaScript程式的另一項基本操作是處理網頁或伺服器端的文字資料。
像其它語言裡一樣，我們使用`string`表示文字資料型別。
和JavaScript一樣，可以使用雙引號（`"`）或單引號（`'`）表示字串。

```typescript
var name: string = "bob";
name = 'smith';
```

你還可以使用*Template Literals*，它可以定義多行文字和內嵌運算式。使用\`(重音符號，ESC下方按鈕)包圍文字，表示此串文字為Template String。在Template String中可以任意使用`${variable}`放置變數。

```TypeScript
var name: string = `Gene`;
var age: number = 37;
var sentence: string = `Hello, my name is ${ name }.

I'll be ${ age + 1 } years old next month`.;
```

這與下面定義`sentence`的方式效果相同：

```TypeScript
var sentence: string = "Hello, my name is " + ".\n\n" + "I'll be " + (age + 1) + " years old next month.";
```

### <a name="1.4"></a>Array

TypeScript像JavaScript一樣可以運算陣列元素。有兩種方式可以定義陣列。第一種，可以在元素後面接上`[]`，表示由此型別元素為一個陣列：

```typescript
var list: number[] = [1, 2 ,3];
```

第二種方式是使用陣列泛型，`Array<元素型別>`：

```typescript
var list: Array<number> = [1, 2, 3];
```

### <a name="1.5"></a>列舉

`enum`型別是對JavaScript標準資料型別的一個擴充。像C#等其它語言一樣，使用列舉型別可以為一組數值賦予友善的名稱。

```typescript
enum Color {Red, Green, Blue};
var c: Color = Color.Green;
```

預設情況下，從`0`開始為元素編號。你也可以手動的指定成員的數值。例如，我們將上面的例子改成從`1`開始編號：

```typescript
enum Color {Red = 1, Green, Blue};
var c: Color = Color.Green;
```

或者，全部都採用手動賦值：

```typescript
enum Color {Red = 1, Green = 2, Blue = 4};
var c: Color = Color.Green;
```

列舉型別提供的一個便利性是你可以由列舉的值得到它的名稱。例如，我們知道列舉數值為2，但是不確定它映射到Color裡的哪個名稱，我們可以找出對應的名稱：

```typescript
enum Color {Red = 1, Green, Blue};
var colorName: string = Color[2];

alert(colorName);  // Green
```

### <a name="1.6"></a>Any

有時，我們可能會想要給在撰寫程式碼時並不清楚的變數指定其型別。這些值可能來自於動態的內容，比如來自使用者或協力廠商函式庫。這種情況下，我們不希望型別檢查器對這些值進行檢查或者說讓它們直接通過編譯階段的檢查。那麼我們可以使用`any`型別來標記這些變數：

```typescript
var notSure: any = 4;
notSure = "maybe a string instead";
notSure = false; // okay，definitely a boolean
```

在對現有JavaScript程式碼進行改寫的時候，`any`型別是十分有用的，它允許你在編譯時選擇性擇性地包含或移除型別檢查。

當你只知道資料型別的一部分時，`any`型別也是有用的。比如，你有一個陣列，它包含了不同的資料型別：

```typescript
var list: any[] = [1, true, "free"];

list[1] = 100;
```

### <a name="1.7"></a>Void

某種程度上來說，`void`型別與`any`型別是相反的，它表示沒有任何型別。當一個函數沒有回傳值時，你通常會見到其回傳型別是`void`：

```typescript
function warnUser(): void {
    alert("This is my warning message");
}
```

## <a name="2"></a>介面

TypeScript的核心原則之一是對值所具有的`形體`進行型別檢查。它有時被稱做“鴨子型別”(duck typing)或“結構性子型別”(structural subtyping)。在TypeScript裡，介面的作用就是為這些型別命名和為你的程式碼或協力廠商程式碼定義契約。

### <a name="2.1"></a>介面初探

下面通過一個簡單範例來觀察介面是如何工作的：

```TypeScript
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label);
}

var myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);
```

型別檢查器會查看`printLabel`的呼叫。`printLabel`有一個參數，並且要求這個物件參數有一個名為`label`型別為`string`的屬性。需要注意的是，我們傳入的物件參數實際上會包含很多屬性，但是編譯器只會檢查那些必需的屬性是否存在，並且其型別是否符合。

下面我們覆寫上面的例子，這次使用介面來描述：必須包含一個`label`屬性且型別為`string`：

```typescript
interface LabelledValue {
  label: string;
}

function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}

var myObj = {size: 10, label: "Size 10 Object"};
printLabel(myObj);
```

`LabelledValue`介面就好比一個名稱，用來描述上面例子裡的要求。它代表了有一個`label`屬性且型別為`string`的物件。需要注意的是，我們在這裡並不能像在其它語言裡一樣，說傳給`printLabel`的物件實現了這個介面。*我們只會去關注值的外形。只要傳入的物件滿足上面提到的必要條件，那麼它就是被允許的。*

還有一點值得提的是，型別檢查器不會去檢查屬性的順序，只要相應的屬性存在並且型別也是對的就可以。

### <a name="2.2"></a>選擇性屬性

介面裡的屬性不全都是必需的。
有些是只在某些條件下存在，或者根本不存在。
選擇性屬性在應用“option bags”模式時很常用，即給函數傳入的參數物件中只有部分屬性賦值了。

下面是應用了“option bags”的例子：

```TypeScript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.color;
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```

帶有選擇性屬性的介面與普通的介面定義差不多，只是在選擇性屬性名稱定義的後面加一個`?`符號。

選擇性屬性的好處之一是可以對可能存在的屬性進行預定義，好處之二是可以捕獲使用了不存在的屬性時的錯誤。
比如，我們故意將拼寫錯誤的屬性名傳入`createSquare`，就會得到一個錯誤提示。

```typescript
interface SquareConfig {
  color?: string;
  width?: number;
}

function createSquare(config: SquareConfig): {color: string; area: number} {
  var newSquare = {color: "white", area: 100};
  if (config.color) {
    newSquare.color = config.collor;  // 型別檢查器會查出這個拼寫錯誤
  }
  if (config.width) {
    newSquare.area = config.width * config.width;
  }
  return newSquare;
}

var mySquare = createSquare({color: "black"});
```

### <a name="2.3"></a>函數型別

介面能夠描述JavaScript中物件擁有的各種各樣的形體。
除了描述帶有屬性的普通物件外，介面也可以描述函數型別。

為了使用介面表示函數型別，我們需要給介面定義一個呼叫簽章。它就像是一個只有參數列表和回傳型別的函式定義。

```typescript
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```

這樣定義後，我們可以像使用其它介面一樣使用這個函數型別的介面。下例展示了如何建立一個函數型別的變數，並將一個同型別的函數賦值給這個變數。

```typescript
var mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
  var result = source.search(subString);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

對於函數型別的型別檢查來說，函數的參數名稱不需要與介面裡定義的名稱相符合。
比如，我們使用下面的程式碼覆寫上面的例子：

```typescript
var mySearch: SearchFunc;
mySearch = function(src: string, sub: string) {
  var result = src.search(sub);
  if (result == -1) {
    return false;
  }
  else {
    return true;
  }
}
```

函數的參數會逐一進行檢查，要求對應位置上的參數型別是相容的。
函數的回傳型別是通過其回傳值推斷出來的（此例是`false`和`true`）。
如果讓這個函數回傳`number`或`string`，型別檢查器會警告我們函數的回傳型別與`SearchFunc`介面中的定義不相符。

### <a name="2.4"></a>陣列型別

與使用介面描述函數型別差不多，我們也可以描述陣列型別。
陣列型別具有一個`index`型別表示索引的型別，還有一個相應的回傳型別表示通過索引得到的元素的型別。

```typescript
interface StringArray {
  [index: number]: string;
}

var myArray: StringArray;
myArray = ["Bob", "Fred"];
```

支援兩種索引型別：`string`和`number`。陣列可以同時使用這兩種索引型別，但是有一個限制，數值索引回傳值的型別必須是字串索引回傳值的型別的子型別。

索引簽名能夠很好的描述陣列和`dictionary`模式，它們也要求所有屬性要與回傳型別相符合。下面的例子裡，length屬性與一般的索引回傳型別不符合，所以型別檢查器給出一個錯誤提示：

```typescript
interface Dictionary {
  [index: string]: string;
  length: number;    // error, the type of 'length' is not a subtype of the indexer
}
```

### <a name="2.5"></a>類別型別

#### <a name="2.5.1"></a>實現介面

與C#或Java裡介面的基本作用一樣，TypeScript也能夠用它來明確的強制一個類別去符合某種契約。

```typescript
interface ClockInterface {
    currentTime: Date;
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

你也可以在介面中描述一個方法，在類別裡實現它，如同下面的`setTime`方法一樣：

```typescript
interface ClockInterface {
    currentTime: Date;
    setTime(d: Date);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    setTime(d: Date) {
        this.currentTime = d;
    }
    constructor(h: number, m: number) { }
}
```

介面描述了類別的`public`部分，而不是`public`和`private`兩部分。它不會幫你檢查類別是否具有某些`private`成員。

#### <a name="2.5.2"></a>類別靜態部分與執行個體部分的區別

當你操作類別和介面的時候，你要知道類別是具有兩個型別的：靜態部分的型別和執行個體的型別。你會注意到，當你用建構式簽章去定義一個介面並試圖定義一個類別去實現這個介面時會得到一個錯誤：

```typescript
interface ClockInterface {
    new (hour: number, minute: number);
}

class Clock implements ClockInterface  {
    currentTime: Date;
    constructor(h: number, m: number) { }
}
```

這裡因為當一個類別實現了一個介面時，只對其執行個體部分進行型別檢查。constructor存在於類別的靜態部分，所以不在檢查的範圍內。

取而代之，我們應該直接操作類別的`靜態`部分。看下面的例子：

```typescript
interface ClockStatic {
    new (hour: number, minute: number);
}

class Clock {
    currentTime: Date;
    constructor(h: number, m: number) { }
}

var cs: ClockStatic = Clock;
var newClock = new cs(7, 30);
```

### <a name="2.6"></a>擴充介面

和類別一樣，介面也可以互相擴充。擴充介面時會將其它介面裡的屬性複製到這個介面裡，因此允許你把介面拆分成單獨的可重覆使用的組件。

```typescript
interface Shape {
    color: string;
}

interface Square extends Shape {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
```

一個介面可以繼承多個介面，建立出多個介面的合成介面。

```typescript
interface Shape {
    color: string;
}

interface PenStroke {
    penWidth: number;
}

interface Square extends Shape, PenStroke {
    sideLength: number;
}

var square = <Square>{};
square.color = "blue";
square.sideLength = 10;
square.penWidth = 5.0;
```

### <a name="2.7"></a>混合型別

先前我們提過，介面能夠描述JavaScript裡豐富的型別。因為JavaScript其動態靈活的特點，有時你會希望一個物件可以同時具有上面提到的多種型別。

一個例子就是，一個物件可以同時做為函數和物件使用，並帶有額外的屬性。

```typescript
interface Counter {
    (start: number): string;
    interval: number;
    reset(): void;
}

var c: Counter;
c(10);
c.reset();
c.interval = 5.0;
```

使用協力廠商函式庫的時候，你可能會像上面那樣去定義完整的型別。

## <a name="3"></a>類別

傳統的JavaScript程式使用函數和基於原型的繼承來建立可重覆使用的組件，但這對於熟悉使用物件導向方式的程式師來說有些棘手，因為他們應用的是基於類別的繼承並且物件是從類別構建出來的。從JavaScript的下個版本ECMAScript6開始，JavaScript程式將可以使用這種基於類別的物件導向方法。在TypeScript裡，我們允許開發者現在就使用這些特性，並且編譯後的JavaScript可以在所有主流流覽器和平臺上運行，而不需要等到下個JavaScript版本。

### <a name="3.1"></a>類別

下面看一個類別的例子：

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter = new Greeter("world");
```

如果你使用過C#或Java，你會對這種語法非常熟悉。
我們宣告一個`Greeter`類別。這個類別有3個成員：一個叫做`greeting`的屬性，一個建構函數和一個`greet`方法。

你會注意到，我們在參考任何一個類別成員的時候都用了`this`。
它表示我們訪問的是類別的成員。

最後一行，我們使用`new`構造了Greeter類別的一個執行個體。
它會呼叫之前定義的建構函式，建立一個`Greeter`型別的新物件，並執行建構函式初始化它。

### <a name="3.2"></a>繼承

在TypeScript裡，我們可以使用常用的物件導向模式。
當然，基於類別的程式設計中最基本的模式是允許使用繼承來擴充一個類別。

看下面的例子：

```typescript
class Animal {
    name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number = 0) {
        alert(`${this.name} moved ${distanceInMeters}m.`);
    }
}

class Snake extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 5) {
        alert("Slithering...");
        super.move(distanceInMeters);
    }
}

class Horse extends Animal {
    constructor(name: string) { super(name); }
    move(distanceInMeters = 45) {
        alert("Galloping...");
        super.move(distanceInMeters);
    }
}

var sam = new Snake("Sammy the Python");
var tom: Animal = new Horse("Tommy the Palomino");

sam.move();
tom.move(34);
```

這個例子展示了TypeScript中繼承的一些特徵，與其它語言類似。
我們使用`extends`來建立子類別。你可以看到`Horse`和`Snake`類別是父類別`Animal`的子類別，並且可以訪問其屬性和方法。

這個例子也說明了，在子類別裡可以覆寫父類別的方法。
`Snake`類別和`Horse`類別都建立了`move`方法，覆寫了從`Animal`繼承來的`move`方法，使得`move`方法根據不同的類別而具有不同的功能。

### <a name="3.3"></a>Private/Public修飾符

#### <a name="3.3.1"></a>預設為Public

在上面的例子裡，我們可以自由的訪問程式裡定義的成員。
如果你對其它語言中的類別比較瞭解，就會注意到我們在之前的程式碼裡並沒有使用`public`來做修飾；例如，C#要求必須明確地使用`public`指定成員是可見的。
在TypeScript裡，每個成員預設為`public`的。

你仍然可以使用`public`明確地指定其訪問型別，並且這確實是一個最佳實踐。我們可以用下面的方式來覆寫上面的`Animal`類：

```typescript
class Animal {
    public name: string;
    constructor(theName: string) { this.name = theName; }
    move(distanceInMeters: number) {
        alert(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

#### <a name="3.3.2"></a>理解`private`

當成員被標記成`private`時，它就不能在宣告它的類別之外進行訪問。比如：

```TypeScript
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

new Animal("Cat").name; // Error: 'name' is private;
```

TypeScript使用的是結構型型別系統。
當我們比較兩種不同的型別時，並不在乎它們從哪兒來的，如果它們中每個成員的型別都是相容的，我們就認為它們的型別是相容的。

然而，當我們比較帶有`private`或`protected`成員的型別的時候，情況就不同了。如果其中一個型別裡包含一個`private`成員，那麼只有當另外一個型別中也存在這樣一個`private`成員， 並且它們是來自同一處宣告時，我們才認為這兩個型別是相容的。對於`protected`成員也使用這個規則。

下面來看一個例子，詳細的解釋了這點：

```typescript
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}

class Rhino extends Animal {
    constructor() { super("Rhino"); }
}

class Employee {
    private name:string;
    constructor(theName: string) { this.name = theName; }
}

var animal = new Animal("Goat");
var rhino = new Rhino();
var employee = new Employee("Bob");

animal = rhino;
animal = employee; //error: Animal and Employee are not compatible
```

這個例子中有`Animal`和`Rhino`兩個類別，`Rhino`是`Animal`類別的子類別。
還有一個`Employee`類別，型別的結構上看上去與`Animal`是相同的。
我們建立了幾個這些類別的執行個體，並互相賦值來看看會發生什麼。
因為`Animal`和`Rhino`共用了來自`Animal`裡的私有成員定義`private name: string`，因此它們是相容的。
然而`Employee`卻不是這樣。當把`Employee`賦值給`Animal`的時候，得到一個錯誤，說它們的型別不相容。
儘管`Employee`裡也有一個私有成員`name`，但它明顯不是`Animal`裡面定義的那個。

#### <a name="3.3.3"></a>理解`protected`

`protected`修飾符與`private`修飾符的行為很相似，但有一點不同，`protected`成員在衍生類別中仍然可以訪問。例如：

```TypeScript
class Person {
    protected name: string;
    constructor(name: string) { this.name = name; }
}

class Employee extends Person {
    private department: string;
    
    constructor(name: string, department: string) {
        super(name);
        this.department = department;
    }
    
    public getElevatorPitch() {
        return `Hello, my name is ${this.name} and I work in ${this.department}.`;
    }
}

var howard = new Employee("Howard", "Sales");
console.log(howard.getElevatorPitch());
```

注意，我們不能在`Person`類別外使用`name`，但是我們仍然可以通過`Employee`類別的執行個體方法來訪問，因為`Employee`是由`Person`衍生出來的。

#### <a name="3.3.4"></a>參數屬性

在上面的例子中，我們不得不定義一個私有成員`name`和一個建構函式參數`theName`，並且立刻給`name`和`theName`賦值。這種情況經常會遇到。*參數屬性*可以方便地讓我們在一個地方定義並初始化一個成員。下面的例子是對之前`Animal`類的修改版，使用了參數屬性：

```typescript
class Animal {
    constructor(private name: string) { }
    move(distanceInMeters: number) {
        alert(`${this.name} moved ${distanceInMeters}m.`);
    }
}
```

注意看我們是如何捨棄了`theName`，僅在建構函式裡使用`private name: string`參數來建立和初始化`name`成員。
我們把宣告和賦值合併至一處。

參數屬性通過給建構函式參數添加一個訪問限定詞來宣告。
使用`private`限定一個參數屬性會宣告並初始化一個私有成員；對於`public`和`protected`來說也是一樣。

### <a name="3.4"></a>存取器

TypeScript支援getters/setters來截取對物件成員的訪問。它能説明你有效的控制對物件成員的訪問。

下面來看如何把一類別改寫成使用‘get’和‘set’。首先是沒用使用存取器的例子：

```typescript
class Employee {
    fullName: string;
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

我們可以隨意的設置fullName，這是非常方便的，但是這也可能會帶來麻煩。

下面這個版本裡，我們先檢查使用者密碼是否正確，然後再允許其修改employee。
我們把對fullName的直接訪問改成了可以檢查密碼的`set`方法。我們也加了一個`get`方法，讓上面的例子仍然可以工作。

```typescript
var passcode = "secret passcode";

class Employee {
    private _fullName: string;

    get fullName(): string {
        return this._fullName;
    }
  
    set fullName(newName: string) {
        if (passcode && passcode == "secret passcode") {
            this._fullName = newName;
        }
        else {
            alert("Error: Unauthorized update of employee!");
        }
    }
}

var employee = new Employee();
employee.fullName = "Bob Smith";
if (employee.fullName) {
    alert(employee.fullName);
}
```

我們可以修改一下密碼，來驗證一下存取器是否是工作的。當密碼不對時，會提示我們沒有權限去修改employee。

注意：若要使用存取器，編譯器要求設置的輸出目標為ECMAScript 5。

### <a name="3.5"></a>靜態屬性

到目前為止，我們只討論了類別的執行個體成員，那些僅當類別被產生實體的時候才會被初始化的屬性。
我們也可以建立類別的靜態成員，這些屬性存在於類別本身上面而不是類別的執行個體上。
在這個例子裡，我們使用`static`定義`origin`，因為它是所有`Grid`類別都會用到的屬性。
每個執行個體想要訪問這個屬性的時候，都要在origin前面加上類別名稱。
如同在執行個體屬性上使用`this.`首碼來訪問屬性一樣，這裡我們使用`Grid.`來訪問靜態屬性。

```typescript
class Grid {
    static origin = {x: 0, y: 0};
    calculateDistanceFromOrigin(point: {x: number; y: number;}) {
        var xDist = (point.x - Grid.origin.x);
        var yDist = (point.y - Grid.origin.y);
        return Math.sqrt(xDist * xDist + yDist * yDist) / this.scale;
    }
    constructor (public scale: number) { }
}

var grid1 = new Grid(1.0);  // 1x scale
var grid2 = new Grid(5.0);  // 5x scale

alert(grid1.calculateDistanceFromOrigin({x: 10, y: 10}));
alert(grid2.calculateDistanceFromOrigin({x: 10, y: 10}));
```

### <a name="3.6"></a>進階技巧

#### <a name="3.6.1"></a>建構函式

當你在TypeScript裡定義類別的時候，實際上同時定義了很多東西。首先是類別的執行個體的型別。

```typescript
class Greeter {
    greeting: string;
    constructor(message: string) {
        this.greeting = message;
    }
    greet() {
        return "Hello, " + this.greeting;
    }
}

var greeter: Greeter;
greeter = new Greeter("world");
alert(greeter.greet());
```

在這裡，我們寫了`var greeter: Greeter`，意思是`Greeter`類執行個體的型別是`Greeter`。
這對於用過其它物件導向語言的程式師來講已經是老習慣了。

我們也建立了一個叫做*建構函式*的值。
這個函數會在我們使用`new`建立類執行個體的時候被呼叫。
下面我們來看看，上面的程式碼被編譯成JavaScript後是什麼樣子的：

```javascript
var Greeter = (function () {
    function Greeter(message) {
        this.greeting = message;
    }
    Greeter.prototype.greet = function () {
        return "Hello, " + this.greeting;
    };
    return Greeter;
})();

var greeter;
greeter = new Greeter("world");
alert(greeter.greet());
```

上面的程式碼裡，‘var Greeter’將被賦值為建構函式。當我們使用‘new’並執行這個函數後，便會得到一個類別的執行個體。這個建構函式也包含了類別的所有靜態屬性。換個角度說，我們可以認為類別具有執行個體部分與靜態部分這兩個部分。

讓我們來改寫一下這個例子，看看它們之前的區別：

```typescript
class Greeter {
    static standardGreeting = "Hello, there";
    greeting: string;
    greet() {
        if (this.greeting) {
            return "Hello, " + this.greeting;
        }
        else {
            return Greeter.standardGreeting;
        }
    }
}

var greeter1: Greeter;
greeter1 = new Greeter();
alert(greeter1.greet());

var greeterMaker: typeof Greeter = Greeter;
greeterMaker.standardGreeting = "Hey there!";
var greeter2:Greeter = new greeterMaker();
alert(greeter2.greet());
```

這個例子裡，`greeter1`與之前看到的一樣。
我們產生實體`Greeter`類，並使用這個物件。
與我們之前看到的一樣。

再之後，我們直接使用類別。
我們建立了一個叫做`greeterMaker`的變數。
這個變數保存了這個類別或者說保存了類別建構函式。
然後我們使用`typeof Greeter`，意思是取得Greeter類別的型別，而不是執行個體的型別。
或者確切的說，"告訴我`Greeter`識別字的型別"，也就是建構函式的型別。
這個型別包含了類別的所有靜態成員和建構函式。
之後，就和前面一樣，我們在`greeterMaker`上使用`new`，建立`Greeter`的執行個體。

#### <a name="3.6.2"></a>把類別當做介面使用

如上一節裡所講的，類別定義會建立兩個東西：類別執行個體的型別和一個建構函式。因為類別可以建立出型別，所以你能夠在可以使用介面的地方使用類別。

```typescript
class Point {
    x: number;
    y: number;
}

interface Point3d extends Point {
    z: number;
}

var point3d: Point3d = {x: 1, y: 2, z: 3};
```

## <a name="4"></a>命名空間和模組

這節會列出多種在TypeScript裡組織程式碼的方法。
我們將介紹命名空間（之前叫做“內部模組(Internal modules)”）和模組（之前叫做“外部模組(External modules)”）並且會討論在什麼樣的場合下適合使用它們以及怎樣使用它們。
我們也會涉及到一些高級主題，如怎麼使用外部模組，當使用TypeScript模組時如何避免常見的陷阱。

### 一個關於術語的注意事項

我們剛剛提及了“內部模組”和“模組”。
如果你對這個術語感到似曾相識，那麼一定要注意在TypeScript1.5裡，它們的命名發生了變化。
“內部模組”變成了“命名空間”。
“外部模組”變成了簡單的“模組”，為了與ECMAScript 2015的術語保持一致。

並且，任何使用`module`關鍵字宣告內部模組的地方，都可以使用`namespace`關鍵字來代替。

這樣就避免了新使用者可能把它們搞混了。

### 第一步

我們先來寫一段程式並將在整個小節中都使用這個例子。
我們定義幾個簡單的字串驗證器，好比你會使用它們來驗證表單裡的使用者輸入或驗證外部資料。

#### 所有的驗證器都放在一個檔案裡

```typescript
interface StringValidator {
    isAcceptable(s: string): boolean;
}

var lettersRegexp = /^[A-Za-z]+$/;
var numberRegexp = /^[0-9]+$/;

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
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: StringValidator; } = {};
validators['ZIP code'] = new ZipCodeValidator();
validators['Letters only'] = new LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

### 使用命名空間

隨著我們增加更多的驗證器，我們想要將它們組織在一起來保持對它們的追蹤記錄並且不用擔心與其它物件產生命名衝突。
我們把驗證器包裹到一個命名空間內，而不是把它們放在全域命名空間下。

這個例子裡，我們把所有驗證器相關的型別都放到一個叫做`Validation`的命名空間裡。
因為我們想讓這些介面和類別在命名空間外也是可訪問的，所以我們需要使用`export`。
相反的，變數`lettersRegexp`和`numberRegexp`是具體實現，所以沒有匯出，因此它們在命名空間外是不能訪問的。
在檔案末尾的測試程式碼裡，我們需要限制型別名稱，因為這是在命名空間外訪問，比如`Validation.LettersOnlyValidator`。

#### 使用命名空間的驗證器

```typescript
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }

    var lettersRegexp = /^[A-Za-z]+$/;
    var numberRegexp = /^[0-9]+$/;

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
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```
### 分割成多檔

當應用變得越來越大時，我們需要將程式碼分散到不同的檔案中以便於維護。

### <a name="4.1"></a>多檔中的命名空間

現在，我們把`Validation`命名空間分割成多個檔案。
儘管是不同的檔案，它們仍是同一個命名空間，並且在使用的時候就如同它們在一個檔案中定義的一樣。
因為不同檔案之間存在依賴關係，所以我們加入了參考標籤(`/// <reference path="" />`)來告訴編譯器檔之間的關聯。
我們的測試程式碼保持不變。

#### Validation.ts

```typescript
namespace Validation {
    export interface StringValidator {
        isAcceptable(s: string): boolean;
    }
}
```

#### LettersOnlyValidator.ts

```typescript
/// <reference path="Validation.ts" />
namespace Validation {
    var lettersRegexp = /^[A-Za-z]+$/;
    export class LettersOnlyValidator implements StringValidator {
        isAcceptable(s: string) {
            return lettersRegexp.test(s);
        }
    }
}
```

#### ZipCodeValidator.ts

```typescript
/// <reference path="Validation.ts" />
namespace Validation {
    var numberRegexp = /^[0-9]+$/;
    export class ZipCodeValidator implements StringValidator {
        isAcceptable(s: string) {
            return s.length === 5 && numberRegexp.test(s);
        }
    }
}
```

#### Test.ts

```typescript
/// <reference path="Validation.ts" />
/// <reference path="LettersOnlyValidator.ts" />
/// <reference path="ZipCodeValidator.ts" />

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: Validation.StringValidator; } = {};
validators['ZIP code'] = new Validation.ZipCodeValidator();
validators['Letters only'] = new Validation.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

當涉及到多檔時，我們必須確保所有編譯後的程式碼都被載入了。
我們有兩種方式。

第一種方式，把所有的輸入檔案編譯為一個輸出檔案，需要使用`--out`標記：

```Shell
tsc --out sample.js Test.ts
```

編譯器會根據來源程式碼裡的參考標籤自動地對輸出進行排序。你也可以單獨地指定每個檔案。

```Shell
tsc --out sample.js Validation.ts LettersOnlyValidator.ts ZipCodeValidator.ts Test.ts
```

第二種方式，我們可以編譯每一個檔案（預設方式），那麼每個原始檔案都會對應產生一個JavaScript檔。
然後，在頁面上通過`<script>`標籤把所有產生的JavaScript檔案按正確的順序參考進來，比如：

#### MyTestPage.html（摘錄部分）

```html
<script src="Validation.js" type="text/javascript" />
<script src="LettersOnlyValidator.js" type="text/javascript" />
<script src="ZipCodeValidator.js" type="text/javascript" />
<script src="Test.js" type="text/javascript" />
```

### <a name="4.2"></a>使用模組

TypeScript中同樣存在模組(Modules)的概念。
模組會在兩種情況下被用到：Node.js或require.js。
對於沒有使用Node.js和require.js的應用來說是不需要使用模組的，最好使用上面介紹的命名空間的方式來組織程式碼。

使用模組時，不同檔案之間的關係是通過檔案層級的導入和匯出來指定的。
在TypeScript裡，任何含有頂層`import`和`export`的檔案都會被視為模組。

下面，我們把之前的例子改寫成使用模組。
注意，我們不再使用`module`關鍵字 - 檔案本身會被視為一個模組並以檔案名稱來區分。

參考標籤用`import`語句來代替，指明了模組之前的依賴關係。
`import`語句有兩部分：模組在當前檔案中的名稱，`require`關鍵字指定了依賴模組的路徑：

```typescript
import someMod = require('someModule');
```

我們通過頂層的`export`關鍵字指出了哪些物件在模組外是可見的，如同使用`export`定義命名空間的公開介面一樣。

為了編譯，我們必須在命令列上指明產生模組的目標型別。對於Node.js，使用*--module commonjs*。對於require.js，使用`--module amd`。比如：

```Shell
ts --module commonjs Test.ts
```

編譯的時候，每個模組會變成一個單獨的檔案。如同參考標籤，編譯器會按照*import*語句編譯對應的檔案。

#### Validation.ts

```typescript
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

#### LettersOnlyValidator.ts

```typescript
import validation = require('./Validation');
var lettersRegexp = /^[A-Za-z]+$/;
export class LettersOnlyValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
```

#### ZipCodeValidator.ts

```typescript
import validation = require('./Validation');
var numberRegexp = /^[0-9]+$/;
export class ZipCodeValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
```

#### Test.ts

```typescript
import validation = require('./Validation');
import zip = require('./ZipCodeValidator');
import letters = require('./LettersOnlyValidator');

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: validation.StringValidator; } = {};
validators['ZIP code'] = new zip.ZipCodeValidator();
validators['Letters only'] = new letters.LettersOnlyValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

### 產生模組程式碼

根據編譯時指定的目的模組型別，編譯器會產生對應的程式碼，或者是適合Node.js（commonjs）或者是適合require.js（AMD）模組載入系統的程式碼。
想要瞭解更多關於`define`和`require`函數的使用方法，請閱讀相應模組載入器的說明文件。

這個例子展示了在導入匯出階段使用的名稱是怎麼轉換成模組載入程式碼的。

#### SimpleModule.ts

```typescript
import m = require('mod');
export var t = m.something + 1;
```

#### AMD/RequireJS SimpleModule.js

```javascript
define(["require"，"exports"，"mod"]，function(require, exports, m) {
    exports.t = m.something + 1;
});
```

#### CommonJS / Node SimpleModule.js

```javascript
var m = require('mod');
exports.t = m.something + 1;
```

### <a name="4.3"></a>Export =

在上面的例子中，使用驗證器的時候，每個模組只匯出一個值。
像這種情況，在驗證器物件前面再加上限定名稱就顯得累贅了，最好是直接使用一個識別字。

`export =`語法指定了模組匯出的單個物件。
它可以是類別，介面，模組，函數或列舉型別。
當import的時候，直接使用模組匯出的識別字，不再需要其它限定名稱。

下面，我們簡化驗證器的實現，使用`export =`語法使每個模組匯出單一物件。
這會簡化對模組的使用 - 我們可以用`zipValidator`代替`zip.ZipCodeValidator`。

#### Validation.ts

```typescript
export interface StringValidator {
    isAcceptable(s: string): boolean;
}
```

#### LettersOnlyValidator.ts

```typescript
import validation = require('./Validation');
var lettersRegexp = /^[A-Za-z]+$/;
class LettersOnlyValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return lettersRegexp.test(s);
    }
}
export = LettersOnlyValidator;
```

#### ZipCodeValidator.ts

```typescript
import validation = require('./Validation');
var numberRegexp = /^[0-9]+$/;
class ZipCodeValidator implements validation.StringValidator {
    isAcceptable(s: string) {
        return s.length === 5 && numberRegexp.test(s);
    }
}
export = ZipCodeValidator;
```

#### Test.ts

```typescript
import validation = require('./Validation');
import zipValidator = require('./ZipCodeValidator');
import lettersValidator = require('./LettersOnlyValidator');

// Some samples to try
var strings = ['Hello', '98052', '101'];
// Validators to use
var validators: { [s: string]: validation.StringValidator; } = {};
validators['ZIP code'] = new zipValidator();
validators['Letters only'] = new lettersValidator();
// Show whether each string passed each validator
strings.forEach(s => {
    for (var name in validators) {
        console.log('"' + s + '" ' + (validators[name].isAcceptable(s) ? ' matches ' : ' does not match ') + name);
    }
});
```

### <a name="4.4"></a>別名

另一種簡化模組操作的方法是使用`import q = x.y.z`給常用的模組起一個簡短的名稱。
不要與`import x = require('name')`用來載入模組的語法弄混了，這裡的語法是為指定的符號建立一個別名。
你可以用這種方法為任意識別字建立別名，也包括導入的模組中的物件。

#### 建立別名基本方法

```typescript
namespace Shapes {
    export namespace Polygons {
        export class Triangle { }
        export class Square { }
    }
}

import polygons = Shapes.Polygons;
var sq = new polygons.Square(); // Same as 'new Shapes.Polygons.Square()'
```

注意，我們並沒有使用`require`關鍵字，而是直接使用導入符號的限定名稱賦值。
這與使用`var`相似，但它還適用於型別和導入的具有命名空間含義的符號。
重要的是，對於值來講，`import`會產生與原始符號不同的參考，所以改變別名的值並不會影響原始變數的值。

### <a name="4.5"></a>選擇性模組的載入與其它進階載入的場景

有些時候，你只想在某種條件下才去載入一個模組。
在TypeScript裡，我們可以使用下面的方式來實現它以及其它高級載入的場景，直接呼叫模組載入器而不必擔心型別安全問題。

編譯器能探測出一個模組是否在產生的JavaScript裡被使用到了。
對於那些只做為型別系統部分使用的模組來講，不會產生對應`require程式碼`。
挑出未使用的參考有益於性能優化，同時也允許選擇性的載入模組。

這種模式的核心是`import id = require('...')`讓我們可以訪問模組匯出的型別。
模組載入是動態呼叫的（通過`require`），像下面`if`語句展示的那樣。
它利用了挑出對未使用參考的優化，模組只在需要的時候才去載入。
為了讓這種方法可行，通過`import`定義的符號只能在表示型別的位置使用（也就是說那段程式碼永遠不會被編譯產生JavaScript）。

為了確保使用正確，我們可以使用`typeof`關鍵字。
在要求是型別的位置使用`typeof`關鍵字時，會得到型別值，在這個例子裡得到的是模組的型別。

#### Dynamic Module Loading in Node.js

```typescript
declare var require;
import Zip = require('./ZipCodeValidator');
if (needZipValidation) {
    var x: typeof Zip = require('./ZipCodeValidator');
    if (x.isAcceptable('.....')) { /* ... */ }
}
```

#### Sample: Dynamic Module Loading in require.js

```typescript
declare var require;
import Zip = require('./ZipCodeValidator');
if (needZipValidation) {
    require(['./ZipCodeValidator'], (x: typeof Zip) => {
        if (x.isAcceptable('...')) { /* ... */ }
    });
}
```

### <a name="4.6"></a>使用其它JavaScript函式庫

為了描述不是用TypeScript寫的函式庫的型別，我們需要對函式庫公開的API進行宣告。
由於大部分函式庫只提供少數的頂層物件，命名空間和模組是用來表示它們是一個好辦法。
我們叫它宣告不是對執行環境的定義。
通常會在`.d.ts`裡寫這些定義。
如果你熟悉C/C++，你可以把它們當做`.h`文件。
讓我們看一些例子。

#### <a name="4.6.1"></a>外部命名空間

流行的函式庫D3在全域物件`d3`裡定義它的功能。
因為這個函式庫通過一個`<script>`標籤載入（不是通過模組載入器），它的宣告檔使用命名空間來定義它的型別。
為了讓TypeScript編譯器識別它的型別，我們使用外部命名空間宣告。
比如，我們像下面這樣寫：

#### D3.d.ts (部分摘錄)

```typescript
declare namespace d3 {
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

declare var d3: D3.Base;
```

#### <a name="4.6.2"></a>外來的模組

在Node.js裡，大多數的任務可以通過載入一個或多個模組來完成。
我們可以使用頂層export宣告來為每個模組定義各自的`.d.ts`檔案，但全部放在一個大的檔案中會更方便。
為此，我們把模組名稱用引號括起來，方便之後的import。
例如：

#### node.d.ts (部分摘錄)

```typescript
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
    export var sep: string;
}
```

現在我們可以`///<reference path="node.d.ts"/>`, 然後使用`import url = require('url');`載入這個模組。

```typescript
///<reference path="node.d.ts"/>
import url = require("url");
var myUrl = url.parse("http://www.typescriptlang.org");
```

### <a name="4.7"></a>命名空間和模組的陷阱

這一節，將會介紹使用命名空間和模組時常見的陷阱和怎麼去避免它。

#### <a name="4.7.1"></a>對模組使用`/// <reference>`

一個常見的錯誤是使用`/// <reference>`參考模組檔案，應該使用import。
要理解這之間的不同，我們首先應該弄清編譯器是怎麼找到模組的型別資訊的。

首先，根據`import x = require(...);`宣告尋找`.ts`文件。
這個檔案應該是使用了頂層import或export宣告的執行檔。

其次，與前一步相似，去尋找`.d.ts`檔，不同的是它不是執行檔而是宣告檔（同樣具有頂層的import或export宣告）。

最後，在`declare`的模組裡尋找名稱符合的“外來模組的宣告”。

#### myModules.d.ts

```typescript
// In a .d.ts file or .ts file that is not a module:
declare module "SomeModule" {
    export function fn(): string;
}
```

#### myOtherModule.ts

```typescript
/// <reference path="myModules.d.ts" />
import m = require("SomeModule");
```

這裡的參考標籤指定了外來模組的位置。
這就是一些Typescript例子中參考node.d.ts的方法。

#### <a name="4.7.2"></a>不必要的命名空間

如果你想把命名空間轉換為模組，它可能會像下面這個檔案：

#### shapes.ts

```typescript
export namespace Shapes {
    export class Triangle { /* ... */ }
    export class Square { /* ... */ }
}
```

頂層的模組`Shapes`包裹了`Triangle`和`Square`。
這對於使用它的人來說是讓人迷惑和討厭的：

#### shapeConsumer.ts

```typescript
import shapes = require('./shapes');
var t = new shapes.Shapes.Triangle(); // shapes.Shapes?
```

TypeScript裡模組的一個特點是不同的模組永遠也不會在相同的作用域內使用相同的名稱。
因為使用模組的人會為它們命名，所以完全沒有必要把匯出的符號包裹在一個命名空間裡。

再次重申，不應該對模組使用命名空間，使用命名空間是為了提供邏輯分組和避免命名衝突。
模組檔本身已經是一個邏輯分組，並且它的名稱是由導入這個模組的程式碼指定，所以沒有必要為匯出的物件增加額外的模組層。

下面是改進的例子：

#### shapes.ts

```typescript
export class Triangle { /* ... */ }
export class Square { /* ... */ }
```

#### shapeConsumer.ts

```typescript
import shapes = require('./shapes');
var t = new shapes.Triangle(); 
```

#### <a name="4.7.3"></a>模組的取捨

就像每個JS檔案對應一個模組一樣，TypeScript裡模組檔案與產生的JS檔案也是一一對應的。
這會產生一個效果，就是無法使用`--out`來讓編譯器合併多個模組檔為一個JavaScript檔案。

## <a name="5"></a>函數

函數是JavaScript應用程式的基礎。
它説明你實現抽象層，模仿類別，資訊隱藏和模組。
在TypeScript裡，雖然已經支持類別，命名空間和模組，但函數仍然是主要的定義*行為*的地方。
TypeScript為JavaScript函數添加了額外的功能，讓我們可以更容易的使用。

### <a name="5.1"></a>Functions

和JavaScript一樣，TypeScript函數可以建立有名稱的函數和匿名函數。
你可以隨意選擇適合應用程式的方式，不論是定義一系列API函數還是只使用一次的函數。

通過下面的例子可以迅速回想起這兩種JavaScript中的函數：

```javascript
//Named function
function add(x, y) {
    return x + y;
}

//Anonymous function
var myAdd = function(x, y) { return x + y; };
```

在JavaScript裡，函數可以使用函數體外部的變數。當函數這麼做時，我們說它‘捕獲’(capture)了這些變數。至於為什麼可以這樣做以及其中的利弊超出了本文的範圍，但是深刻理解這個機制對學習JavaScript和TypeScript會很有幫助。

```javascript
var z = 100;

function addToZ(x, y) {
    return x + y + z;
}
```

### <a name="5.2"></a>函數型別

#### <a name="5.2.1"></a>為函式定義型別

讓我們為上面那個函數添加型別：

```typescript
function add(x: number, y: number): number {
    return x + y;
}

var myAdd = function(x: number, y: number): number { return x + y; };
```

我們可以給每個參數添加型別之後再為函數本身添加回傳型別。TypeScript能夠根據回傳語句自動推斷出回傳型別，因此我們通常省略它。

#### <a name="5.2.2"></a>撰寫函數型別

現在我們已經為函數指定了型別，下面讓我們寫出函數的完整型別。

```typescript
var myAdd: (x:number, y:number) => number = 
    function(x: number, y: number): number { return x + y; };
```

函數型別包含兩部分：參數型別和回傳型別。當寫出完整函數型別的時候，這兩部分都是需要的。我們以參數列表的形式寫出參數型別，為每個參數指定一個名稱和型別。這個名稱只是為了增加可讀性。我們也可以這麼寫：

```typescript
var myAdd: (baseValue:number, increment:number)=>number = 
    function(x: number, y: number): number { return x+y; };
```

只要參數型別是符合的，那麼就認為它是有效的函數型別，而不在乎參數名稱是否正確。

對於回傳值，我們在函數和回傳型別之前使用(=>)符號，使之清晰明瞭。如之前提到的，回傳型別是函數型別的必要部分，如果函數沒有回傳任何值，你也必須指定回傳型別為‘void’而不能留空。

函數的型別只是由參數型別和回傳值組成的。函數中使用的‘捕獲’變數不會體現在型別裡。實際上，這些變數是函數的隱藏狀態並不是組成API的一部分。

#### <a name="5.2.3"></a>推斷型別

嘗試這個例子的時候，你會發現如果你在設定陳述式的一邊指定了型別但是另一邊沒有型別的話，TypeScript編譯器會自動識別出型別：

```typescript
// myAdd has the full function type
var myAdd = function(x: number, y: number): number { return x + y; };

// The parameters 'x' and 'y' have the type number
var myAdd: (baseValue:number, increment:number) => number = 
    function(x, y) { return x + y; };
```

這叫做依‘情境歸類’(contextual typing)，是型別推論的一種。
它説明我們更好地為程式指定型別。

### <a name="5.3"></a>選擇性參數和預設參數

不同於JavaScript，TypeScript裡每個函數參數都是必須的。
這並不是指參數一定是個非`null`值，而是編譯器檢查使用者是否為每個參數都傳入了值。
編譯器還會假設只有這些參數會被傳遞進入函數。
簡短地說，傳遞給函數的參數數量必須與函數期望的參數數量一致。

```typescript
function buildName(firstName: string, lastName: string) {
    return firstName + " " + lastName;
}

var result1 = buildName("Bob");  //error, too few parameters
var result2 = buildName("Bob", "Adams", "Sr.");  //error, too many parameters
var result3 = buildName("Bob", "Adams");  //ah, just right
```

JavaScript裡，每個參數都是選擇性的，可傳值可不傳值。沒傳值的時候，它的值就是undefined。在TypeScript裡我們可以在參數名稱旁使用‘?’實現選擇性參數的功能。比如，我們想讓last name是選擇性的：

```typescript
function buildName(firstName: string, lastName?: string) {
    if (lastName)
        return firstName + " " + lastName;
    else
        return firstName;
}

var result1 = buildName("Bob");  //works correctly now
var result2 = buildName("Bob", "Adams", "Sr.");  //error, too many parameters
var result3 = buildName("Bob", "Adams");  //ah, just right
```

選擇性參數必須在必須跟在必須參數後面。如果上例我們想讓first name是選擇性的，那麼就必須調整它們的位置，把first name放在後面。

TypeScript裡，我們還可以為選擇性參數設置預設值。仍然修改上例，把last name的預設值設置為“Smith”。

```typescript
function buildName(firstName: string, lastName = "Smith") {
    return firstName + " " + lastName;
}

var result1 = buildName("Bob");  //works correctly now, also
var result2 = buildName("Bob", "Adams", "Sr.");  //error, too many parameters
var result3 = buildName("Bob", "Adams");  //ah, just right
```

和選擇性參數一樣，帶預設值的參數也要放在必須參數後面。

選擇性參數與預設值參數共享參數型別，如下所示：

```typescript
function buildName(firstName: string, lastName?: string) {
```

和

```typescript
function buildName(firstName: string, lastName = "Smith") {
```

共享同樣的型別`(firstName: string, lastName?: string) => string`。預設參數的預設值消失了，只保留了它是一個選擇性參數的資訊。

### <a name="5.4"></a>剩餘參數

必要參數，預設參數和選擇性參數有個共通點：它們表示某一個參數。有時，你想同時操作多個參數，或者你並不知道會有多少參數傳遞進來。在JavaScript裡，你可以使用arguments來訪問所有傳入的參數。

在TypeScript裡，你可以把所有參數收集到一個變數裡：

```typescript
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

var employeeName = buildName("Joseph", "Samuel", "Lucas", "MacKinzie");
```

剩餘參數會被當做個數不限的選擇性參數。可以一個都沒有，同樣也可以有任意個數。編譯器建立參數陣列，名稱是你在省略號（...）後面給定的名稱，你可以在函數體內使用這個陣列。

這個省略號也會在帶有剩餘參數的函數型別定義上使用到。

```typescript
function buildName(firstName: string, ...restOfName: string[]) {
  return firstName + " " + restOfName.join(" ");
}

var buildNameFun: (fname: string, ...rest: string[]) => string = buildName;
```

### <a name="5.5"></a>Lambda運算式和使用‘this’

JavaScript裡‘this’的工作機制對JavaScript程式師來說已經是老生常談了。的確，學會如何使用它絕對是JavaScript程式設計中的一件大事。由於TypeScript是JavaScript的超集合，TypeScript程式師也需要弄清‘this’工作機制並且當有bug的時候能夠找出錯誤所在。‘this’的工作機制可以單獨寫一本書了，並確已有人這麼做了。在這裡，我們只介紹一些基礎知識。

JavaScript裡，*‘this’的值在函數被呼叫的時候才會指定*。這是個既強大又靈活的特點，但是你需要花點時間弄清楚函式呼叫的脈絡是什麼。眾所皆知這不是一件很簡單的事，特別是函數當做回呼函數使用的時候。

下面看一個例子：

```javascript
var deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        return function() {
            var pickedCard = Math.floor(Math.random() * 52);
            var pickedSuit = Math.floor(pickedCard / 13);
      
            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

var cardPicker = deck.createCardPicker();
var pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

如果我們運行這個程式，會發現它並沒有彈出對話方塊而是報錯了。
因為`createCardPicker`回傳的函數裡的`this`被設置成了`window`而不是`deck`物件。
當你呼叫`cardPicker()`時會發生這種情況。這裡沒有對`this`進行動態繫結因此為window。（注意在嚴格模式下，會是undefined而不是window）。

為了解決這個問題，我們可以在函數被回傳時就繫結好正確的`this`。
這樣的話，無論之後怎麼使用它，都會參考綁定的‘deck’物件。

我們把函數運算式變為使用lambda運算式（ () => {} ）。這樣就會在函數建立的時候就指定了‘this’值，而不是在函式呼叫的時候。

```typescript
var deck = {
    suits: ["hearts", "spades", "clubs", "diamonds"],
    cards: Array(52),
    createCardPicker: function() {
        // Notice: the line below is now a lambda, allowing us to capture 'this' earlier
        return () => {
            var pickedCard = Math.floor(Math.random() * 52);
            var pickedSuit = Math.floor(pickedCard / 13);
      
            return {suit: this.suits[pickedSuit], card: pickedCard % 13};
        }
    }
}

var cardPicker = deck.createCardPicker();
var pickedCard = cardPicker();

alert("card: " + pickedCard.card + " of " + pickedCard.suit);
```

為瞭解更多關於`this`的資訊，請閱讀Yahuda Katz的[Understanding JavaScript Function Invocation and "this"](http://yehudakatz.com/2011/08/11/understanding-javascript-function-invocation-and-this/)。

> 補充：[#Javascript：this用法整理](https://software.intel.com/zh-cn/blogs/2013/10/09/javascript-this)

### <a name="5.6"></a>多載

JavaScript本身是個動態語言。
JavaScript裡函數根據傳入不同的參數而回傳不同型別的資料是很常見的。

```typescript
var suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        var pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        var pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

var myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
var pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

var pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

‘pickCard’方法根據傳入參數的不同會回傳兩種不同的型別。如果傳入的是代表紙牌的物件，函數作用是從中抓一張牌。如果使用者想抓牌，我們告訴他抓到了什麼牌。但是這怎麼在型別系統裡表示呢。

方法是為同一個函數提供多個函數型別定義來進行函數多載。編譯器會根據這個清單去處理函數的呼叫。下面我們來多載‘pickCard’函數。

```typescript
var suits = ["hearts", "spades", "clubs", "diamonds"];

function pickCard(x: {suit: string; card: number; }[]): number;
function pickCard(x: number): {suit: string; card: number; };
function pickCard(x): any {
    // Check to see if we're working with an object/array
    // if so, they gave us the deck and we'll pick the card
    if (typeof x == "object") {
        var pickedCard = Math.floor(Math.random() * x.length);
        return pickedCard;
    }
    // Otherwise just let them pick the card
    else if (typeof x == "number") {
        var pickedSuit = Math.floor(x / 13);
        return { suit: suits[pickedSuit], card: x % 13 };
    }
}

var myDeck = [{ suit: "diamonds", card: 2 }, { suit: "spades", card: 10 }, { suit: "hearts", card: 4 }];
var pickedCard1 = myDeck[pickCard(myDeck)];
alert("card: " + pickedCard1.card + " of " + pickedCard1.suit);

var pickedCard2 = pickCard(15);
alert("card: " + pickedCard2.card + " of " + pickedCard2.suit);
```

這樣改變後，多載的`pickCard`函數在呼叫的時候會進行正確的型別檢查。

為了讓編譯器能夠選擇正確的檢查型別，它與JavaScript裡的處理流程相似。
它尋找多載列表，嘗試使用第一個多載定義。
如果符合的話就使用這個。
因此，在定義多載的時候，一定要把最精確的定義放在最前面。

注意，`function pickCard(x): any`並不是多載列表的一部分，因此這裡只有兩個多載：一個是接收物件另一個接收數值。
以其它參數呼叫`pickCard`會產生錯誤。

## <a name="6"></a>泛型

軟體工程中，我們不僅要建立一致定義良好的API，同時也要考慮可重用性。組件不僅能夠支援當前的資料型別，同時也能支援未來的資料型別，這在建立大型系統時為你提供了十分靈活的功能。

在像C#和Java這樣的語言中，可以使用‘泛型’來建立可重用的元件，一個元件可以支援多種型別的資料。這樣使用者就可以以自己的資料型別來使用元件。

### <a name="6.1"></a>泛型Hello World

下面來建立第一個使用泛型的例子：identity函數。這個函數會回傳任何傳入它的值。你可以把這個函數當成是‘echo’命令。

不用泛型的話，這個函數可能是下面這樣：

```typescript
function identity(arg: number): number {
    return arg;
}
```

或者，我們使用‘any’型別來定義函數：

```typescript
function identity(arg: any): any {
    return arg;
}
```

雖然使用`any`型別後這個函數已經能接收任何型別的arg參數，但是卻丟失了一些資訊，當函式回傳時是什麼型別。
如果我們傳入一個數值，我們只知道任何型別的值都有可能被回傳。

因此，我們需要一種方法使回傳值的型別與傳入參數的型別是相同的。
這裡，我們使用了*型別變數*，它是一種特殊的變數，只用於表示型別而不是值。

```typescript
function identity<T>(arg: T): T {
    return arg;
}
```

我們給identity添加了型別變數‘T’。‘T’幫助我們捕獲使用者傳入的型別（比如：number），之後我們就可以使用這個型別。之後我們再次使用了‘T’當做回傳型別。現在我們可以知道參數型別與回傳型別是相同的了。

我們把這個版本的identity函數叫做泛型，它可以適用於多個型別。不像使用‘any’，它不會丟失資訊。同時也可以像第一個例子那樣，傳入數值型別並回傳數值型別。

我們定義了泛型函數後，可以用兩種方法使用。第一種是，傳入所有的參數，包含型別參數：

```typescript
var output = identity<string>("myString");  // type of output will be 'string'
```

這裡我們明確的指定了‘T’是字串型別，並做為一個參數傳給函數，使用了<>括起來。

第二種方法更普遍。利用了型別推論，編譯器會根據傳入的參數自動地幫助我們確定T的型別：

```typescript
var output = identity("myString");  // type of output will be 'string'
```

注意我們並沒用`<>`明確的指定型別，編譯器看到了`myString`，把`T`設置為此型別。
型別推論幫助我們保持程式碼精簡和高可讀性。如果編譯器不能夠自動地推斷出型別的話，只能像上面那樣明確的傳入T的型別，在一些複雜的情況下，這是可能出現的。

### <a name="6.2"></a>使用泛型變數

使用泛型建立像`identity`這樣的泛型函數時，編譯器要求你在函數本體必須正確的使用這個通用的型別。換句話說，你必須把這些參數當做是任意或所有型別。

看下之前`identity`例子：

```typescript
function identity<T>(arg: T): T {
    return arg;
}
```

如果我們想同時列印出arg的‘length’屬性值，很可能會這樣做：

```typescript
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}
```

如果這麼做，編譯器會報錯說我們使用了`arg`的`.length`屬性，但是沒有地方指明`arg`具有這個屬性。
記住，這些型別變數代表的是任意型別，所以使用這個函數的人可能傳入的是個數值，而數值是沒有`.length`屬性的。

現在假設我們想操作`T`型別是陣列而不直接是`T`。由於我們操作的是陣列，所以`.length`屬性是應該存在的。
我們可以像建立其它陣列一樣建立這個陣列：

```typescript
function loggingIdentity<T>(arg: T[]): T[] {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

你可以這樣理解`loggingIdentity`的型別：泛型函數`loggingIdentity`，接收型別參數`T`，和函數`arg`，它是個元素型別是`T`的陣列，並回傳元素型別是`T`的陣列。
如果我們傳入數值陣列，將回傳一個數值陣列，因為此時`T`的的型別為`number`。
這可以讓我們把泛型變數T當做型別的一部分使用，而不是整個型別，增加了靈活性。

我們也可以這樣實現上面的例子：

```typescript
function loggingIdentity<T>(arg: Array<T>): Array<T> {
    console.log(arg.length);  // Array has a .length, so no more error
    return arg;
}
```

使用過其它語言的話，你可能對這種語法已經很熟悉了。在下一節，會介紹如何建立自訂泛型像Array<T>一樣。

### <a name="6.3"></a>泛型型別

上一節，我們建立了identity泛型函數，可以適用於不同的型別。在這節，我們研究一下函數本身的型別，以及如何建立泛型介面。

泛型函數的型別與非泛型函數的型別沒什麼不同，只是有一個型別參數在最前面，像函式宣告一樣：

```typescript
function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: <T>(arg: T)=>T = identity;
```

我們也可以使用不同的泛型參數名稱，只要在數量上和使用方式上能對應上即可。

```typescript
function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: <U>(arg: U)=>U = identity;
```

我們還可以使用帶有呼叫簽章的物件實字型別(object literal type)來定義泛型函數：

```typescript
function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: { <T>(arg: T): T} = identity;
```

這引導我們去寫第一個泛型介面了。我們把上面例子裡的物件實字拿出來做為一個介面：

```typescript
interface GenericIdentityFn {
    <T>(arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: GenericIdentityFn = identity;
```

一個相似的例子，我們可能想把泛型參數當作整個介面的一個參數。
這樣我們就能清楚的知道使用的具體是哪個泛型型別（比如：`Dictionary<string>而不只是Dictionary`）。
這樣介面裡的其它成員也能知道這個參數的型別了。

```typescript
interface GenericIdentityFn<T> {
    (arg: T): T;
}

function identity<T>(arg: T): T {
    return arg;
}

var myIdentity: GenericIdentityFn<number> = identity;
```

我們並沒有描述泛型函數，而是使用一個非泛型函數簽章作為泛型型別一部分。當我們使用GenericIdentityFn的時候，我也得傳入一個型別參數來指定泛型型別（這個例子是：number），鎖定了之後程式碼裡使用的型別。

除了泛型介面，我們還可以建立泛類別。
注意，無法建立列舉泛型和模組泛型。

### <a name="6.4"></a>泛型類別

泛型類別看上去與泛型介面差不多。泛型類別使用<>括起泛型型別，跟在類別名稱後面。

```typescript
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

var myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```

‘GenericNumber’類別的使用是十分直觀的，並且你應該注意到了我們並不限制只能使用數值型別。也可以使用字串或其它更複雜的型別。

```typescript
var stringNumeric = new GenericNumber<string>();
stringNumeric.zerValue = "";
stringNumeric.add = function(x, y) { return x + y; };

alert(stringNumeric.add(stringNumeric.zeroValue, "test"));
```

與介面一樣，直接把泛型型別放在類別後面，可以幫助我們確認類別的所有屬性都在使用相同的型別。

我們在[類別](#類別)那節說過，類別有兩部分：靜態部分和執行個體部分。泛型類別指的是執行個體部分的型別，所以類別的靜態屬性不能使用這個泛型型別。

### <a name="6.5"></a>泛型約束

你應該會記得之前的一個例子，我們有時候想操作某型別的一組值，並且我們知道這組值具有什麼樣的屬性。在‘loggingIdentity’例子中，我們想訪問‘arg’的‘length’屬性，但是編譯器並不能證明每種型別都有‘length’屬性，所以就報錯了。

```typescript
function loggingIdentity<T>(arg: T): T {
    console.log(arg.length);  // Error: T doesn't have .length
    return arg;
}
```

相比於操作任意型別，我們想要限制函數去處理任意帶有‘length’屬性的型別。只要傳入的型別有這個屬性，就允許通過。所以我們必須對T定義約束。

為此，我們定義一個介面來描述約束條件。建立一個包含‘length’屬性的介面，使用這個介面和extends關鍵字來實現約束。

```typescript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);  // Now we know it has a .length property, so no more error
    return arg;
}
```

現在這個泛型函數被定義了約束，因此它不再是適用於任意型別：

```typescript
loggingIdentity(3);  // Error, number doesn't have a .length property
```

我們需要傳入符合約束型別的值，必須包含必須的屬性：

```typescript
loggintIdentity({ length: 10, value: 3 });
```

#### <a name="6.5.1"></a>在泛型約束中使用型別參數

有時候，我們需要使用型別參數去約束另一個型別參數。比如，

```typescript
function find<T, U extends Findable<T>>(n: T, s: U) {  // errors because type parameter used in contraint
  // ... 
}
find(giraffe, myAnimals);
```

可以通過下面的方法來實現，覆寫上面的程式碼，

```typescript
function find<T>(n: T, s: Findable<T>) {
  // ...
}
find(giraffe, myAnimals);
```

注意：上面兩種寫法並不完全等同，因為第一段程式的回傳值可能是U，而第二段程式卻沒有這一限制。

#### <a name="6.5.2"></a>在泛型裡使用類別型別

在TypeScript使用泛型建立工廠函數時，需要參考建構函式的類別型別。比如，

```typescript
function create<T>(c: {new(): T;}): T {
    return new c();
}
```

一個更高級的例子，使用原型屬性推斷並約束建構函式與類別執行個體的關係。

```typescript
class BeeKeeper {
    hasMask: boolean;
}

class ZooKeeper {
    nametag: string; 
}

class Animal {
    numLegs: number;
}

class Bee extends Animal {
    keeper: BeeKeeper;
}

class Lion extends Animal {
    keeper: ZooKeeper;
}

function findKeeper<A extends Animal, K> (a: {new(): A; 
    prototype: {keeper: K}}): K {

    return a.prototype.keeper;
}

findKeeper(Lion).nametag;  // typechecks!
```

## <a name="7"></a>常見錯誤

下面列出了一些在使用TypeScript和編譯器的時候常見的錯誤

### <a name="7.1"></a>常見疑難問題

#### <a name="7.1.1"></a>"tsc.exe" exited with error code 1.

**Fixes:**

檢查檔案編碼是不是UTF-8 - [https://typescript.codeplex.com/workitem/1587](https://typescript.codeplex.com/workitem/1587)

#### <a name="7.1.2"></a>external module XYZ cannot be resolved

**Fixes:**

檢查模組路徑的大小寫 - [https://typescript.codeplex.com/workitem/2134](https://typescript.codeplex.com/workitem/2134)

## <a name="8"></a>Mixins

除了傳統的物件導向繼承方式，還有一種流行的從可重用元件中建立類的方式，就是通過聯合一個簡單類的程式碼。你可能在Scala這樣的語言裡對mixins已經熟悉了，它在JavaScript中也是很流行的。

### <a name="8.1"></a>混入範例

下面的程式碼演示了如何在TypeScript裡使用mixins。後面我們還會解釋這段程式碼是怎麼工作的。

```typescript
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }
 
}
 
// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}
 
class SmartObject implements Disposable, Activatable {
    constructor() {
        setInterval(() => console.log(this.isActive + " : " + this.isDisposed), 500);
    }
 
    interact() {
        this.activate();
    }
 
    // Disposable
    isDisposed: boolean = false;
    dispose: () => void;
    // Activatable
    isActive: boolean = false;
    activate: () => void;
    deactivate: () => void;
}
applyMixins(SmartObject, [Disposable, Activatable])
 
var smartObj = new SmartObject();
setTimeout(() => smartObj.interact(), 1000);
 
////////////////////////////////////////
// In your runtime library somewhere
////////////////////////////////////////

function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name];
        })
    }); 
}
```

### <a name="8.2"></a>理解這個例子

程式碼裡首先定義了兩個類，它們將做為mixins。
可以看到每個類都只定義了一個特定的行為或功能。
稍後我們使用它們來建立一個新類，同時具有這兩種功能。

```typescript
// Disposable Mixin
class Disposable {
    isDisposed: boolean;
    dispose() {
        this.isDisposed = true;
    }
 
}
 
// Activatable Mixin
class Activatable {
    isActive: boolean;
    activate() {
        this.isActive = true;
    }
    deactivate() {
        this.isActive = false;
    }
}
```

下面建立一個類，結合了這兩個mixins。下面來看一下具體是怎麼操作的。

```typescript
class SmartObject implements Disposable, Activatable {}
```

首先應該注意到的是，沒使用‘extends’而是使用‘implements’。把類當成了介面，僅使用Disposable和Activatable的型別而非其實現。這意味著我們需要在類裡面實現介面。但是這是我們在用mixin時想避免的。

我們可以這麼做來達到目的，為將要mixin進來的屬性方法建立出占位元屬性。這告訴編譯器這些成員在運行時是可用的。這樣就能使用mixin帶來的便利，雖說需要提前定義一些占位元屬性。

```typescript
// Disposable
isDisposed: boolean = false;
dispose: () => void;
// Activatable
isActive: boolean = false;
activate: () => void;
deactivate: () => void;
```

最後，把mixins混入定義的類，完成全部實現部分。

```typescript
applyMixins(SmartObjet, [Disposable, Activatable])
```

最後，建立這個説明函數，幫我們做混入操作。它會遍歷mixins上的所有屬性，並複製到目標上去，把之前的占位元屬性替換成真正的實現程式碼。

```typescript
function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name];
        })
    }); 
}
```

## <a name="9"></a>宣告合併

TypeScript有一些獨特的概念，有的是因為我們需要描述JavaScript頂層物件的型別發生了什麼變化。這其中之一叫做‘宣告合併’。理解了這個概念對於你使用TypeScript去操作現有的JavaScript是大有幫助的。同時，也開啟通往更多高級抽象概念的大門。

首先，在瞭解怎麼進行宣告合併之前，讓我們先看一下什麼叫做‘宣告合併’。

在這個手冊裡，宣告合併是指編譯器會把兩個相同的名稱的宣告合併成一個單獨的宣告。合併後的宣告同時具有那兩個被合併的宣告的特性。宣告合併不限於只合併兩個，任意數量都可以。

### <a name="9.1"></a>基礎概念

Typescript裡宣告可用於三個地方：命名空間/模組，型別或者值。建立命名空間/模組的宣告可以通過（.）識別字訪問其中的型別。建立型別的宣告就是用給定的名稱建立相應型別。最後，建立值的宣告是在產生的JavaScript裡存在的那部分（比如：函數和變數）。

| Declaration Type | Namespace | Type | Value |
|------------------|:---------:|:----:|:-----:|
| Module           |     X     |      |   X   |
| Class            |           |   X  |   X   |
| Interface        |           |   X  |       |
| Function         |           |      |   X   |
| Variable         |           |      |   X   |

理解每個宣告建立了什麼，有助於理解當宣告合併時什麼東西被合併了。

### <a name="9.2"></a>合併介面

最簡單最常見的就是合併介面，宣告合併的種類是：介面合併。從根本上說，合併的機制是把各自宣告裡的成員放進一個同名的單一介面裡。

```typescript
interface Box {
    height: number;
    width: number;
}

interface Box {
    scale: number;
}

var box: Box = {height: 5, width: 6, scale: 10};
```

介面中非函數的成員必須是唯一的。如果多個介面中具有相同名稱的非函數成員就會報錯。

對於函數成員，每個同名函式宣告都會被當成這個函數的一個多載。

需要注意的是，介面A與它後面的介面A（把這個介面叫做A'）合併時，A'中的多載函數具有更高的優先順序。

如下例所示：

```typescript
interface Document {
    createElement(tagName: any): Element;
}
interface Document {
    createElement(tagName: string): HTMLElement;
}
interface Document {
    createElement(tagName: "div"): HTMLDivElement; 
    createElement(tagName: "span"): HTMLSpanElement;
    createElement(tagName: "canvas"): HTMLCanvasElement;
}
```

這三個介面合併成一個宣告。注意每組介面裡的宣告順序保持不變，只是靠後的介面會出現在它前面的介面宣告之前。

```typescript
interface Document {
    createElement(tagName: "div"): HTMLDivElement; 
    createElement(tagName: "span"): HTMLSpanElement;
    createElement(tagName: "canvas"): HTMLCanvasElement;
    createElement(tagName: string): HTMLElement;
    createElement(tagName: any): Element;
}
```

### <a name="9.3"></a>合併模組

與介面相似，同名的模組也會合並其成員。模組會建立出命名空間和值，我們需要知道這兩者都是怎麼合併的。

命名空間的合併，模組匯出的同名介面進行合併，構成單一命名空間內含合併後的介面。

值的合併，如果當前已經存在給定名稱的模組，那麼後來的模組的匯出成員會被加到已經存在的那個模組裡。

‘Animals’宣告合併範例：

```typescript
module Animals {
    export class Zebra { }
}

module Animals {
    export interface Legged { numberOfLegs: number; }
    export class Dog { }
}
```

等同於

```typescript
module Animals {
    export interface Legged { numberOfLegs: number; }
    
    export class Zebra { }
    export class Dog { }
}
```

除了這些合併外，你還需要瞭解非匯出成員是如何處理的。非匯出成員僅在其原始存在於的模組（未合併的）之內可見。這就是說合併之後，從其它模組合併進來的成員無法訪問非匯出成員了。

下例提供了更清晰的說明：

```typescript
module Animal {
    var haveMuscles = true;

    export function animalsHaveMuscles() {
        return haveMuscles;
    }
}

module Animal {
    export function doAnimalsHaveMuscles() {
        return haveMuscles;  // <-- error, haveMuscles is not visible here
    }
}
```

因為haveMuscles並沒有匯出，只有animalsHaveMuscles函數共用了原始未合併的模組可以訪問這個變數。doAnimalsHaveMuscles函數雖是合併模組的一部分，但是訪問不了未匯出的成員。

### <a name="9.4"></a>模組與類和函數和列舉型別合併

模組可以與其它型別的宣告進行合併。只要模組的定義符合將要合併型別的定義。合併結果包含兩者的宣告型別。Typescript使用這個功能去實現一些JavaScript裡的設計模式。

首先，嘗試將模組和類合併。這讓我們可以定義內部類。

```typescript
class Album {
    label: Album.AlbumLabel;
}
module Album {
    export class AlbumLabel { }
}
```

合併規則與上面‘合併模組’小節裡講的規則一致，我們必須匯出AlbumLabel類，好讓合併的類能訪問。合併結果是一個類並帶有一個內部類。你也可以使用模組為類增加一些靜態屬性。

除了內部類的模式，你在JavaScript裡，建立一個函數稍後擴充它增加一些屬性也是很常見的。Typescript使用宣告合併來達到這個目的並保證型別安全。

```typescript
function buildLabel(name: string): string {
    return buildLabel.prefix + name + buildLabel.suffix;
}

module buildLabel {
    export var suffix = "";
    export var prefix = "Hello, ";
}

alert(buildLabel("Sam Smith"));
```

相似的，模組可以用來擴充列舉型：

```typescript
enum Color {
    red = 1,
    green = 2,
    blue = 4
}

module Color {
    export function mixColor(colorName: string) {
        if (colorName == "yellow") {
            return Color.red + Color.green;
        }
        else if (colorName == "white") {
            return Color.red + Color.green + Color.blue;
        }
        else if (colorName == "magenta") {
            return Color.red + Color.blue;
        }
        else if (colorName == "cyan") {
            return Color.green + Color.blue;
        }
    }
}
```

### <a name="9.5"></a>非法的合併

並不是所有的合併都被允許。現在，類不能與類合併，變數與型別不能合併，介面與類不能合併。想要模仿類的合併，請參考[Mixins in TypeScript](https://typescript.codeplex.com/wikipage?title=Mixins%20in%20TypeScript&referringTitle=Declaration%20Merging)。

## <a name="10"></a>型別推論

這節介紹TypeScript裡的型別推論。即，型別是在哪裡如何被推斷的。

### <a name="10.1"></a>基礎

TypeScript裡，在有些沒有明確指出型別的地方，型別推論會幫助提供型別。如下面的例子

```typescript
var x = 3;
```

變數x的型別被推斷為數值。這種推斷發生在初始化變數和成員，設置預設參數值和決定函數回傳值時。

大多數情況下，型別推論是直截了當地。後面的小節，我們會流覽型別推論時的細微差別。

### <a name="10.2"></a>最佳通用型別

當需要從幾個運算式中推斷型別時候，會使用這些運算式的型別來推斷出一個最合適的通用型別。例如，

```typescript
var x = [0, 1, null];
```

為了推斷x的型別，我們必須考慮所有元素的型別。這裡有兩種選擇：數值和null。計算通用型別演算法會考慮所有的候選型別，並給出一個相容所有候選型別的型別。

由於最終的通用型別取自候選型別，有些時候候選型別共用相同的通用型別，但是卻沒有一個型別能做為所有候選型別的型別。例如：

```typescript
var zoo = [new Rhino(), new Elephant(), new Snake()];
```

這裡，我們想讓zoo被推斷為`Animal[]`型別，但是這個陣列裡沒有物件是`Animal`型別的，因此不能推斷出這個結果。
為了更正，當候選型別不能使用的時候我們需要明確的指出型別：

```typescript
var zoo: Animal[] = [new Rhino(), new Elephant(), new Snake()];
```

如果沒有找到最佳通用型別的話，型別推論的結果是空物件型別，`{}`。
因為這個型別沒有任何成員，所以訪問其成員的時候會報錯。

### <a name="10.3"></a>上下文型別

TypeScript型別推論也可能按照相反的方向進行。
這被叫做“按上下文歸類”。按上下文歸類會發生在運算式的型別與所處的位置相關時。比如：

```typescript
window.onmousedown = function(mouseEvent) { 
    console.log(mouseEvent.buton);  //<- Error  
};
```

這個例子會得到一個型別錯誤，TypeScript型別檢查器使用`Window.onmousedown`函數的型別來推斷右邊函數運算式的型別。
因此，就能推斷出`mouseEvent`參數的型別了。
如果函數運算式不是在上下文型別的位置，`mouseEvent`參數的型別需要指定為`any`，這樣也不會報錯了。

如果上下文型別運算式包含了明確的型別資訊，上下文的型別被忽略。
覆寫上面的例子：

```typescript
window.onmousedown = function(mouseEvent: any) { 
    console.log(mouseEvent.buton);  //<- Now, no error is given  
};
```

這個函數運算式有明確的參數型別注解，上下文型別被忽略。
這樣的話就不報錯了，因為這裡不會使用到上下文型別。

上下文歸類會在很多情況下使用到。
通常包含函數的參數，賦值運算式的右邊，型別斷言，物件成員和陣列字面量和回傳值語句。
上下文型別也會做為最佳通用型別的候選型別。比如：

```typescript
function createZoo(): Animal[] {
    return [new Rhino(), new Elephant(), new Snake()];
}
```

這個例子裡，最佳通用型別有4個候選者：`Animal`，`Rhino`，`Elephant`和`Snake`。
當然，`Animal`會被做為最佳通用型別。

## <a name="11"></a>型別相容性

TypeScript裡的型別相容性是以結構性子型別來判斷的。結構性型別是完全根據成員關聯型別的一種方式。與正常的型別判斷不同。看下面的例子：

```typescript
interface Named {
    name: string;
}

class Person {
    name: string;
}

var p: Named;
// OK, because of structural typing
p = new Person();
```

在正常使用型別的語言像C#或Java中，這段程式碼會報錯，因為Person類沒有明確說明其實現了Named介面。

TypeScript的結構性子型別是根據JavaScript程式碼的通常寫法來設計的。因為JavaScript裡常用匿名物件像函數運算式或物件字面量，所以用結構性型別系統來描述這些型別比使用正常的型別系統更好。

### <a name="11.0"></a>關於穩定性的注意事項

TypeScript的型別系統允許那些在編譯階段無法否認其安全性的操作。當一個型別系統具有此屬性時，被當做是“不穩定”的。TypeScript裡允許這種不穩定行為發生的地方是經過仔細考慮的。通過這篇文章，我們會解釋什麼時候會發生這種情況和其背景。

### <a name="11.1"></a>開始

TypeScript結構化型別系統的基本規則是，如果x與y相容，那麼y至少具有與x相同的屬性。比如：

```typescript
interface Named {
    name: string;
}

var x: Named;
// y’s inferred type is { name: string; location: string; }
var y = { name: 'Alice', location: 'Seattle' };
x = y;
```

這裡要檢查y是否能賦值給x，編譯器x中的每個屬性，看是否能在y中也找到對應屬性。在這個例子中，y必須包含名稱是‘name’的string型別成員。y滿足條件，因此賦值正確。

檢查函數參數時使用相同的規則：

```typescript
function greet(n: Named) {
    alert('Hello, ' + n.name);
}
greet(y); // OK
```

注意，‘y’有個額外的‘location’屬性，但這不會引發錯誤。只有目標型別（這裡是‘Named’）的成員會被一一檢查是否相容。

這個比較過程是遞迴進行的，檢查每個成員及子成員。

### <a name="11.2"></a>比較兩個函數

比較原始型別和物件型別時是容易理解的，問題是如何判斷兩個函數是相容的。讓我們以兩個函數開始，它們僅有參數列表不同：

```typescript
var x = (a: number) => 0;
var y = (b: number, s: string) => 0;

y = x; // OK
x = y; // Error
```

要查看x是否能賦值給y，首先看它們的參數列表。x的每個參數必須能在y裡找到對應型別的參數。注意的是參數的名稱相同與否無所謂，只看它們的型別。這裡，x的每個參數在y中都能找到對應的參數，所以允許賦值。

第二個賦值錯誤，因為y有個必需的第二個參數，但是x並沒有，所以不允許賦值。

你可能會疑惑為什麼允許‘忽略’參數，像例子y=x中那樣。原因是忽略額外的參數在JavaScript裡是很常見的。例如，Array#forEach給回呼函數傳3個參數：陣列元素，索引和整個陣列。儘管如此，傳入一個隻使用第一個參數的回呼函數也是很有用的：

```typescript
var items = [1, 2, 3];

// Don't force these extra arguments
items.forEach((item, index, array) => console.log(item));

// Should be OK!
items.forEach((item) => console.log(item));
```

下面來看看如何處理回傳型別，建立兩個僅是回傳型別不同的函數：

```typescript
var x = () => ({name: 'Alice'});
var y = () => ({name: 'Alice', location: 'Seattle'});

x = y; // OK
y = x; // Error because x() lacks a location property
```

型別系統強制源函數的回傳型別必須是目標函數回傳型別的子型別。

#### <a name="11.2.1"></a>函數參數雙向協變

當比較函數參數型別時，只有源函數參數能夠賦值給目標函數或反過來才符合成功。這是不穩定的，因為呼叫者可能會被給予一個函數，它接受一個更確切型別，但是呼叫函數使用不那麼確切的型別。實際上，這極少會發生錯誤，並且能夠實現很多JavaScript裡的常見模式。例如：

```typescript
enum EventType { Mouse, Keyboard }

interface Event { timestamp: number; }
interface MouseEvent extends Event { x: number; y: number }
interface KeyEvent extends Event { keyCode: number }

function listenEvent(eventType: EventType, handler: (n: Event) => void) {
    /* ... */
}

// Unsound, but useful and common
listenEvent(EventType.Mouse, (e: MouseEvent) => console.log(e.x + ',' + e.y));

// Undesirable alternatives in presence of soundness
listenEvent(EventType.Mouse, (e: Event) => console.log((<MouseEvent>e).x + ',' + (<MouseEvent>e).y));
listenEvent(EventType.Mouse, <(e: Event) => void>((e: MouseEvent) => console.log(e.x + ',' + e.y)));

// Still disallowed (clear error). Type safety enforced for wholly incompatible types
listenEvent(EventType.Mouse, (e: number) => console.log(e));
```

#### <a name="11.2.2"></a>選擇性參數及剩餘參數

比較函數相容性的時候，選擇性參數與必須參數是可交換的。

當一個函數有剩餘參數時，它被當做無限個選擇性參數。

這對於型別系統來說是不穩定的，但從運行時的角度來看，選擇性參數一般來說是不強制的，因為對於大多數函數來說相當於傳遞了一些‘undefinded’。

有一個好的例子，常見的函數接收一個回呼函數並用對於程式師來說是可預知的參數但對型別系統來說是不確定的參數來呼叫：

```typescript
function invokeLater(args: any[], callback: (...args: any[]) => void) {
    /* ... Invoke callback with 'args' ... */
}

// Unsound - invokeLater "might" provide any number of arguments
invokeLater([1, 2], (x, y) => console.log(x + ', ' + y));

// Confusing (x and y are actually required) and undiscoverable
invokeLater([1, 2], (x?, y?) => console.log(x + ', ' + y));
```

#### <a name="11.2.3"></a>函數多載

對於有多載的函數，源函數的每個多載都要在目標函數上找到對應的函數簽名。這確保了目標函數可以在所有源函數可呼叫的地方呼叫。對於特殊的函數多載簽名不會用來做相容性檢查。

### <a name="11.3"></a>列舉

列舉型別與數值型別相容，並且數值型別與列舉型別相容。不同列舉型別之間是不相容的。比如，

```typescript
enum Status { Ready, Waiting };
enum Color { Red, Blue, Green };

var status = Status.Ready;
status = Color.Green;  //error
```

### <a name="11.4"></a>類

類與物件字面量和介面差不多，但有一點不同：類有靜態部分和執行個體部分的型別。比較兩個類型別的物件時，只有執行個體的成員會被比較。靜態成員和建構函式不在比較的範圍內。

```typescript
class Animal {
    feet: number;
    constructor(name: string, numFeet: number) { }
}

class Size {
    feet: number;
    constructor(numFeet: number) { }
}

var a: Animal;
var s: Size;

a = s;  //OK
s = a;  //OK
```

#### <a name="11.4.1"></a>類的私有成員

私有成員會影響相容性判斷。當類的執行個體用來檢查相容時，如果它包含一個私有成員，那麼目標型別必須包含來自同一個類的這個私有成員。這允許子類賦值給父類，但是不能賦值給其它有同樣型別的類。

### <a name="11.5"></a>泛型

因為TypeScript是結構性的型別系統，型別參數只影響使用其做為型別一部分的結果型別。比如，

```typescript
interface Empty<T> {
}
var x: Empty<number>;
var y: Empty<string>;

x = y;  // okay, y matches structure of x
```

上面程式碼裡，x和y是相容的，因為它們的結構使用型別參數時並沒有什麼不同。把這個例子改變一下，增加一個成員，就能看出是如何工作的了：

```typescript
interface NotEmpty<T> {
    data: T;
}
var x: NotEmpty<number>;
var y: NotEmpty<string>;

x = y;  // error, x and y are not compatible
```

在這裡，泛型型別在使用時就好比不是一個泛型型別。

對於沒指定泛型型別的泛型參數時，會把所有泛型參數當成‘any'比較。然後用結果型別進行比較，就像上面第一個例子。

比如，

```typescript
var identity = function<T>(x: T): T { 
    // ...
}

var reverse = function<U>(y: U): U {
    // ...
}

identity = reverse;  // Okay because (x: any)=>any matches (y: any)=>any
```

### <a name="11.6"></a>高級主題

#### <a name="11.6.1"></a>子型別與賦值

目前為止，我們使用了`相容性`，它在語言規範裡沒有定義。
在TypeScript裡，有兩種型別的相容性：子型別與賦值。
它們的不同點在於，賦值擴充了子型別相容，允許給`any`賦值或從`any`取值和允許數值賦值給列舉型別或列舉型別賦值給數值。

語言裡的不同地方分別使用了它們之中的機制。
實際上，型別相容性是由賦值相容性來控制的甚至在`implements`和`extends`語句裡。
更多資訊，請參閱[TypeScript語言規範](http://go.microsoft.com/fwlink/?LinkId=267121).

## <a name="12"></a>書寫.d.ts文件

當使用外部JavaScript庫或新的宿主API時，你需要一個宣告檔（.d.ts）定義函式庫的shape。這個手冊包含了寫.d.ts檔的高級概念，並帶有一些例子，告訴你怎麼去寫一個宣告檔。

### <a name="12.1"></a>指導與說明

#### <a name="12.1.1"></a>流程

最好從函式庫的文檔開始寫.d.ts檔，而不是程式碼。這樣保證不會被具體實現所干擾，而且相比於JS程式碼更易讀。下面的例子會假設你正在參照文檔寫宣告檔。

#### <a name="12.1.2"></a>命名空間

當定義介面（例如：“options”物件），你會選擇是否將這些型別放進命名空間裡。
這主要是靠主觀判斷 -- 使用的人主要是用這些型別宣告變數和參數，並且型別命名不會引起命名衝突，放在全域命名空間裡更好。
如果型別不是被直接使用，或者沒法起一個唯一的名稱的話，就使用命名空間來避免與其它型別發生衝突。

#### <a name="12.1.3"></a>回呼函數

許多JavaScript庫接收一個函數做為參數，之後傳入已知的參數來呼叫它。當為這些型別與函數簽名的時候，不要把這個參數標記成選擇性參數。正確的思考方式是“會提供什麼樣的參數？”，不是“會使用到什麼樣的參數？”。TypeScript 0.9.7+不會強制這種選擇性參數的使用，參數選擇性的雙向協變可以被外部的linter強制執行。

#### <a name="12.1.4"></a>擴充與宣告合併

寫宣告檔的時候，要記住TypeScript擴充現有物件的方式。你可以選擇用匿名型別或介面類別型的方式宣告一個變數：

**匿名型別var**

```typescript
declare var MyPoint: { x: number; y: number; };
```

**介面類別型var**

```typescript
interface SomePoint { x: number; y: number; }
declare var MyPoint: SomePoint;
```

從使用者角度來講，它們是相同的，但是SomePoint型別能夠通過介面合併來擴充：

```typescript
interface SomePoint { z: number; }
MyPoint.z = 4; // OK
```

是否想讓你的宣告是可擴充的取決於主觀判斷。通常來講，儘量符合library的意圖。

#### <a name="12.1.5"></a>類的分解

TypeScript的類會建立出兩個型別：執行個體型別，定義了型別的執行個體具有哪些成員；建構函式型別，定義了類建構函式具有哪些型別。建構函式型別也被稱做類的靜態部分型別，因為它包含了類的靜態成員。

你可以使用typeof關鍵字來拿到類靜態部分型別，在寫宣告檔時，想要把類明確的分解成執行個體型別和靜態型別時是有用且必要的。

下面是一個例子，從使用者的角度來看，這兩個宣告是等同的：

**標準版**

```typescript
class A {
    static st: string;
    inst: number;
    constructor(m: any) {}
}
```

**分解版**

```typescript
interface A_Static {
    new(m: any): A_Instance;
    st: string;
}
interface A_Instance {
    inst: number;
}
declare var A: A_Static;
```

這裡的利弊如下：

* 標準方式可以使用extends來繼承；分解的類不能。這可能會在未來版本的TypeScript裡改變：是否允許任何的extends運算式
* 都允許之後為類添加靜態成員
* 允許為分解的類再添加執行個體成員，標準版不允許
* 使用分解類的時候，為成員起合理的名稱

#### <a name="12.1.6"></a>命名規則

一般來講，不要給介面加I首碼（比如：IColor）。類為TypeScript裡的介面類別型比C#或Java裡的意義更為廣泛，IFoo命名不利於這個特點。

### <a name="12.2"></a>例子

下面進行例子部分。對於每個例子，先是使用使用方法，然後是型別宣告。如果有多個好的宣告表示方法，會列出多個。

#### <a name="12.2。1"></a>參數物件

**使用方法**

```javascript
animalFactory.create("dog");
animalFactory.create("giraffe", { name: "ronald" });
animalFactory.create("panda", { name: "bob", height: 400 });

// 錯誤：如果提供選項，那麼必須包含name
animalFactory.create("cat", { height: 32 });
```

**型別**

```typescript
namespace animalFactory {
    interface AnimalOptions {
        name: string;
        height?: number;
        weight?: number;
    }
    function create(name: string, animalOptions?: AnimalOptions): Animal;
}
```

#### <a name="12.2.2"></a>帶屬性的函數

**使用方法**

```javascript
zooKeeper.workSchedule = "morning";
zooKeeper(giraffeCage);
```

**型別**

```typescript
// 注意：函數必須在命名空間之前
function zooKeeper(cage: AnimalCage);
namespace zooKeeper {
    var workSchedule: string;
}
```

#### <a name="12.2.3"></a>可以用new呼叫也可以直接呼叫的方法

**使用方法**

```javascript
var w = widget(32, 16);
var y = new widget("sprocket");
// w and y are both widgets
w.sprock();
y.sprock();
```

**型別**

```typescript
interface Widget {
    sprock(): void;
}

interface WidgetFactory {
    new(name: string): Widget;
    (width: number, height: number): Widget;
}

declare var widget: WidgetFactory;
```

#### <a name="12.2.4"></a>全域的/不清楚的Libraries

**使用方法**

```javascript
// Either
import x = require('zoo');
x.open();
// or
zoo.open();
```

**型別**

```typescript
namespace zoo {
  function open(): void;
}

declare module "zoo" {
    export = zoo;
}
```

#### <a name="12.2.5"></a>模組的單個複雜物件

**使用方法**

```javascript
// Super-chainable library for eagles
import eagle = require('./eagle');
// Call directly
eagle('bald').fly();
// Invoke with new
var eddie = new eagle(1000);
// Set properties
eagle.favorite = 'golden';
```

**型別**

```typescript
// Note: can use any name here, but has to be the same throughout this file
declare function eagle(name: string): eagle;
declare namespace eagle {
    var favorite: string;
    function fly(): void;
}
interface eagle {
    new(awesomeness: number): eagle;
}

export = eagle;
```

#### <a name="12.2.6"></a>回呼函數

**使用方法**

```javascript
addLater(3, 4, (x) => console.log('x = ' + x));
```

**型別**

```typescript
// Note: 'void' return type is preferred here
function addLater(x: number, y: number, (sum: number) => void): void;
```

如果你想看其它模式的實現方式，請在[這裡](https://github.com/Microsoft/TypeScript-Handbook/issues)留言！
我們會盡可能地加到這裡來。
