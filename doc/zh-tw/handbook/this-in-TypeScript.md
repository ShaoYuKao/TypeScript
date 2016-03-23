## 介紹

在JavaScript裡（還有TypeScript），`this`關鍵字的行為與其它語言相比大為不同。這可能會很令人吃驚，特別是對於那些使用其它語言的使用者憑藉其直覺來想象`this`關鍵字的行為。

這篇文章會教你怎麼識別及偵錯TypeScript裡的`this`問題，並且提供了一些解決方案和各自的利弊。

## 典型症狀和危險係數

丟失`this`上下文的典型症狀包含：

* 類的某欄位（`this.foo`）為`undefined`，但其它值沒有問題
* `this`的值指向全局的`window`物件而不是類例項物件（在非嚴格模式下）
* `this`的值為`undefined`而不是類例項物件（嚴格模式下）
* 呼叫類方法（`this.doBa()`）失敗，錯誤資訊如“TypeError: undefined is not a function”，“Object doesn't support property or method 'doBar'”或“this.doBar is not a function”

下面的程式碼應該出現在程式中：

* 事件監聽，比如`window.addEventListener('click', myClass.doThing);`
* Promise解決，比如`myPromise.then(myClass.theNextThing);`
* 第三方庫回撥，比如`$(document).ready(myClass.start);`
* 函數回撥，比如`someArray.map(myClass.convert)`
* ViewModel類型的庫裡的類，比如`<div data-bind="click: myClass.doSomething">`
* 可選包裡的函數，比如`$.ajax(url, { success: myClass.handleData })`

## JavaScript裡的`this`究竟是什麼？

已經有大量的文章講述了JavaScript裡`this`關鍵字的危險性。檢視[這裡](http://www.quirksmode.org/js/this.html)，[this one](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/)，或[這裡](http://bjorn.tipling.com/all-this)。

當JavaScript裡的一個函數被呼叫時，你可以按照下面的順序來推斷出`this`指向的是什麼（這些規則是按優先順序順序排列的）：

* 如果這個函數是`function#bind`呼叫的結果，那麼`this`指向的是傳入`bind`的參數
* 如果函數是以`foo.func()`形式呼叫的，那麼`this`值為`foo`
* 如果是在嚴格模式下，`this`將為`undefined`
* 否則，`this`將是全局物件（瀏覽器環境裡為`window`）

這些規則會產生與直覺相反的效果。比如：

```ts
class Foo {
  x = 3;
  print() {
    console.log('x is ' + this.x);
  }
}

var f = new Foo();
f.print(); // Prints 'x is 3' as expected

// Use the class method in an object literal
var z = { x: 10, p: f.print };
z.p(); // Prints 'x is 10'

var p = z.p;
p(); // Prints 'x is undefined'
```

## `this`的危險訊號

你要注意地最大的危險訊號是*在要使用類的方法時沒有立即呼叫它*。任何時候你看到類方法被*引用了*卻沒有使用相同的表示式來*呼叫*時，`this`可能已經不對了。

例子：

```ts
var x = new MyObject();
x.printThing(); // SAFE, method is invoked where it is referenced

var y = x.printThing; // DANGER, invoking 'y()' may not have correct 'this'

window.addEventListener('click', x.printThing, 10); // DANGER, method is not invoked where it is referenced

window.addEventListener('click', () => x.printThing(), 10); // SAFE, method is invoked in the same expression
```

## 修復

可以通過一些方法來保持`this`的上下文。

### 使用例項函數

代替TypeScript裡預設的*原型*方法，你可以使用一個*例項箭頭函數*來定義類成員：

```ts
class MyClass {
    private status = "blah";

    public run = () => { // <-- note syntax here
        alert(this.status);
    }
}
var x = new MyClass();
$(document).ready(x.run); // SAFE, 'run' will always have correct 'this'
```

* 好與壞：這會建立額外的閉包對於每個類例項的每個方法。如果這個方法通常是正常呼叫的，那麼這麼做有點過了。然而，它經常會在回撥函數裡呼叫，讓類例項捕獲到`this`上下文會比在每次呼叫時都建立一個閉包來得更有效率一些。
* 好：其它外部使用者不可能忘記處理`this`上下文
* 好：在TypeScript裡是類型安全的
* 好：如果函數帶參數不需要額外的工作
* 壞：派生類不能通過使用`super`呼叫基類方法
* 壞：在類與使用者之前產生了額外的非類型安全的約束：明確了哪些方法提前綁定了以及哪些沒有

### 本地的胖箭頭

在TypeScrip裡（這裡為了講解添加了一些參數） :

```ts
var x = new SomeClass();
someCallback((n, m) => x.doSomething(n, m));
```

* 好與壞：記憶體/效能上的利弊與例項函數相比正相反
* 好：在TypeScript，100%的類型安全
* 好：在ECMAScript 3裡同樣生效
* 好：你只需要輸入一次例項名
* 壞：你要輸出2次參數名
* 壞：對於可變參數不起作用（'rest'）

### Function.bind

```ts
var x = new SomeClass();
// SAFE: Functions created from function.bind are always preserve 'this'
window.setTimeout(x.someMethod.bind(x), 100);
```

* 好與壞：記憶體/效能上的利弊與例項函數相比正相反
* 好：如果函數帶參數不需要額外的工作
* 壞：目前在TypeScript裡，不是類型安全的
* 壞：只在ECMAScript 5裡生效
* 壞：你要輸入2次例項名
