#### TypeScript

###### 原始类型

* 布尔值

  ```typescript
  let isBoolean: boolean = false
  let createByNewBoolean : boolean = new Boolean(1)
  
  注：new Booelan(1) 依旧为 Boolean值
  ```

* 数值

  ```
  let decLiteral: number = 6;
  let binaryLiteral: number = 0b1010 // 二进制
  let hexLiteral: number = 0xf00d // 十六进制
  let octalLiteral: number = 073 // 十进制
  ```

* 字符串

  ```
  let string: string = 'tom'
  
  let sence = `hello ${string}` // 模板字符串
  ```

* 空值

  ```
  Void()
  function alertname(): void {}
  viod类型值只能赋值为 undefined  和  null ;
  ```

* Null  Undefined

  ```
  let u: undefined = undefiend
  let n: null = null
  
  注 void undefined null 区别在于 undefined 和 null 可以做为值赋予给其他类型, void不行
  ```


* 任意值

  ```
  let anyvalue : any = 'seven'
  
  任意值可被任意类型赋值
  ```

* 未声明类型变量

  ```
  未指定类型会被识别为任意值
  ```

###### 类型推论

```
let mineType = 'seven'
mineType = 7 // 报错

等价于 let mineType: string = 'seven'
```

###### 联合类型

```
let unit : string | number;
unit = 'seven'
unit = 7

可赋予为多种类型的值
```

###### 对象类型

```
// 例子
interface Person {
  name: string;
  age: number;
}

let otm: Person = {
  name: 'otn',
  age: 18
}

let otm: Person = {  // 错误，缺少age
  name: 'otn',
}

// 拓展属性（可选,只读,任意）
interface Person {
  name: string;
  age?: number; (可选属性)
  [propName: string]: string; (任意)
  readonly id: number; (只读)
}
```

###### 数组类型

* [类型 + 方括号]

  ```
  let array: number[] = [1,2,3,4,5]
  let array: number[] = [1,'2',3,4]// 数组内部类型错误
  ```

* 数组泛型( Array<type> )

  ```
  let array: Array<number> = [1,2,3,4,5]
  ```

* 接口数组

  ```
  interface NumberArray {
    [index: number]: number;
  }
  let array: NumberArray = [1,2,3,4,5]
  ```

* 类数组

  ```
  TypeScript常用的自定义的接口：IArguments, NodeList, HTMLCollection
  
  function sum() {
    let args: number[] = arguments; // 类型错误
  }
  
  function sum() {     // 正确声明方式
    let args: {
      [index: number]: number;
      length： number;
      callee: Function;
    } = arguments
  }
  
  function sum() {     // 内置接口定义
    let args: IArguments = arguments;
  }
  ```

* 任意定义值

  ```
  let list :any[] = ['xxl',1,2]
  ```

###### 函数类型

* 基础声明方式 (  输入和输出声明 )

  ```
  function sum(x: number, y: number): number{
    return x + y;
  }
  sum(1, 2, 3) // 多余的参数会报错
  ```

* 函数表达式

  ```
  let mySum: (x: number, y: number) => number = function(x: number, y:number):number{
    return x + y;
  }
  ```

* 接口定义函数形状

  ```
  interface SearchFunc {
    (source: string, subString: string): boolean;
  }
  let mySearch: SearhFunc;
  mySearch = function(source: string, subString:string){
    return source.search(subString) != -1;
  }
  ```

* 参数

  ```
  可选参数
  function selName(firstName: string, lastNam?: string){}
  
  参数默认值
  function defaultValue(firstName: string, lastName: string = 'cat'){}
  
  剩余参数
  function onlyParams(array: any[], ...item: any[]) {}
  ```


###### 断言

* 类型断言只影响Ts编译时的类型，类型断言语句在编译结果中会删除:

  ```
  function toBoolean(something: any): boolean{
    return something as boolean;
  }
  
  toBoolean(1) // 返回值为1
  ```

* 类型断言和类型声明

  ```
  function getCacheDate(key: string): any {
    return (window as any).cache[key]
  }
  
  ```

  #### 进阶

  ###### 类型别名

* 给类型起个新名字

  ```
  type name = string 
  type NameResolver = () => string
  type NameOrResolver = Name | NameResolver
  
  functon getName(n: NameOrResolver): Name{
    if (typeof n === 'string') {
      return n;
    } else {
      return n();
    }
  }
  ```

* 字符串字面量类型

  ```
  type EventNames = 'click' | 'scroll' | 'mousemove';
  
  function handleEvent(ele: Element, event: EventNames) {
    // do something
  }
  
  handEvent(document.getElementById('hello'), 'scroll')
  handEvent(document.getElementById('hello'), 'dbscroll') // 报错, event 不为 EventName
  ```

* 元组

  数组合并相同类型的对象，元组合并不同类型的对象

  ```
  let tom: [string, number] = ['tom', 25]
  
  let tom: [string, number]
  tom[0] = 'xxl'
  
  let tom: [string, number]
  
  tom = ['tom'] // 错误 只能这样赋值：tom = ['tom', 25]
  ```

  越界元素： 当添加越界元素时，它的类型会被限制为元组中每个类型的联合类型

  ```
  let tom: [string, number]
  tom = ['tom', 25]
  tom.push(21) // true
  tom.push(true) // 错误
  ```

  ###### 枚举

* 例子

  ```
  enum Days { Sun, Mon, Tue, Web, Thu, Fri, Sat }
  Days[0] , Days[1], Days[2]...    Sun, Mon, Tue
  
  enum Days { Sun, Mon = 'mon', Tue = 'ss' }
  
  Days[0], Days[1], Days[2] ...   Sum, undefined, undefined
  ```

* 手动赋值

  ```
  enum Days { Sun = 7, Mon = 3, Tue, Web, Thu, Fri, Sat }
  
  console.log(Days['Tue'] === 4) // true
  ```

###### 类

一件事物的抽象特点，包含它的属性和方法

###### 泛型 <T>

* 泛型接口

  ```
  interface Person<T> {
    (name: T): T
  }
  ```

* 泛型类

  ```
  class Person<T> {
    name: T
  }
  ```

* 泛型工具类型

  * typeof ( 获取一个变量声明或对象的类型 )

    ```
    interface Person {
      name: string;
      age: number;
    }
    const sem:Person = { name: 'tim', age:33 }
    type Sem = typeof sem // => Person
    ```

###### 装饰器

* 它是一个表达式
* 表达式执行后，返回一个函数
* 函数的入参分别为target name descriptor
* 执行该函数后，可能返回descriptor对象

###### 构造函数类型

* 包含一个或多个构造签名的对象类型被称为构造函数类型

* 构造函数类型可以使用构造函数类型字面量或包含构造签名的对象类型字面量来编写

  ```
  nwe < T1, T2, ... > (p1, p2, ...) => R
  等价于
  { new < T1, T2, ... > (p1, p2, ...) : R }
  ```

  

###### 类型谓词

* 类型保护函数： 自定义一个类型保护，只需要简单的为这个类型保护定义一个函数即可

* 类型谓词为 法为 `parameterName is Type` 这种   `parameterName` 必须是当前函数签名里的一个参数名

  ```
  interface Bird {
    fly()
    layEggs()
  }
  
  interface Fish {
    fly()
    layEggs()
  }
  
  funtion Pet():Fish | Bird {
    return;
  }
  
  let pet = Pet()
  
  pet.layEggs //      true
  (pet as Fish).swim()  // true
  pet.swim()  // Error
  
  
  /********* 类型谓词   is  ***********/
  function isFish(pet: Fish | Bird): pet is Fish{  
    return (pet as Fish).swim !== undefined
  }
  
  if (isFish(pet)) {
    pet.swim();
  } else{
    pet.fly()
  }
  ```


###### never

never 避免出现新增了联合类型没有对应的实现，目的就是写出类型绝对安全的代码。

```
interface Foo {
  type: 'foo'
}
interface Bar {
  type: 'bar'
}

type a = Foo | Bar

function never(val: All) {
  switch (val.type) {
    case: 'foo':
      // is foo
    case: 'bar':
      // is bar
    default
      // is never
      const ischeck: never = val;
  }
}

never 可以用来作为联合类型对应方式的检测，如果在switch条件中，对ALL所有类型都加以处理，那么当前环境是不会报错的。但是当我们在后续的操作中，加了一个条件例如： type a = Foo | Bar | addSome
但是在 never 函数处理中，并没有对addSome进行类别处理，那么当前代码是会报错的
```

###### 类型

* 函数类型

  ```
  interface Func {
    (name: string, age: number): boolean
  }
  ```

* 索引类型

  ```
  interface Guide{
    [index: number]: string
  }
  ```

  TypeScript支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。

* 接口继承

  当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）

  ```
  class Animal {
    private state: any;
  }
  
  interface Cat extends Animal {
    run() {}
  }
  
  class tim extends Animal{}
  
  class tom extends interface{}  // 缺少state 
  ```

  tim 为Animal 子类所有可以访问state属性， tom 继承Cat 方法，但是缺少类的实现方式state

###### 类

构造函数也可以被标记成 `protected`。 这意味着这个类不能在包含它的类外被实例化，但是能被继承

#### 类

* 定义一件事物的抽象特点，包含它的属性和方法
* 对象：类的实例
* 面向对象：封装、继承、多态
  * 封装：对数据的操作隐藏起来，只暴露接口供外界调用
  * 继承：子类继承父类，子类拥有除了父类的所有特性外，还具有其他特性
  * 多态：继承所产生的相关类，对同一个方法具有不同的响应
* 存取器：改变属性的读取个赋值（get  set）
* 修饰符：修饰限定成员或类型的性质，public  state等
* 抽象类：供其他类继承的基类，无法实例化
* 接口：不同类之间公有的属性或方法

###### 继承

```
class Person { name: string }
class man extends Person {}

class Person { name: string; constructor(age) {}}
class man extends Person { name: string; constructor(age) {super(age)} }
```

派生类包含了一个构造函数，它 *必须*调用 `super()`，它会执行基类的构造函数

###### Private （私有）

```
class Animal {
    private name: string;
    constructor(theName: string) { this.name = theName; }
}
class Cat extends Animal {
  constructor(theName: string) { super(theName) };
  say() { console.log(this.name) }  //  this.name 是不能读取的
}

new Animal("Cat").name; // 错误: 'name' 是私有的.
```

实例是不能够直接读取的

###### protected （保护）

* 保护类性质同私有类，唯一不同点在于 保护类的静态属性能够在继承类中获取

* 保护类不能直接实例化（当构造函数被保护时）

  ```
  class Person {
      protected name: string;
      protected constructor(theName: string) { this.name = theName; }
  }
  // Employee 能够继承 Person
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
  
  let howard = new Employee("Howard", "Sales");
  let john = new Person("John"); // 错误: 'Person' 的构造函数是被保护的.
  ```

###### 存取器

###### 静态属性

###### 抽象类

#### 接口

它是对行为的抽象，而具体如何行动需要由类（classes）去实现（implement）。

TypeScript的核心原则之一是对值所具有的*结构*进行类型检查，接口的作用就是为这些类型命名和为你的代码或第三方代码定义契约

###### 接口例子

```
function printLabel(labelledObj: { label: string }) {
  console.log(labelledObj.label);
}
let myObj = { size: 10, label: "Size 10 Object" };
printLabel(myObj);

等价于-

interface LabelledValue {
  label: string;
}
function printLabel(labelledObj: LabelledValue) {
  console.log(labelledObj.label);
}
```

###### 可选属性（?）

###### 只读属性 （readonly）

###### 函数类型

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义

```
interface SearchFunc {
  /** 调用签名 **/
  (source: string, subString: string): boolean; // 参数列表和返回值
}

/***  use  ***/
let mySearch: SearchFunc;
mySearch = function(src, sub) {
    let result = src.search(sub);
    return result > -1;
}
```

###### 可索引类型

“通过索引得到”的类型，比如`a[10]`或`ageMap["daniel"]`

TypeScript支持两种索引签名：字符串和数字。 可以同时使用两种类型的索引，但是数字索引的返回值必须是字符串索引返回值类型的子类型。 这是因为当使用 `number`来索引时，JavaScript会将它转换成`string`然后再去索引对象。 也就是说用 `100`（一个`number`）去索引等同于使用`"100"`（一个`string`）去索引，因此两者需要保持一致。

```
class Animal {
    name: string;
}
class Dog extends Animal {
    breed: string;
}
// 错误：使用数值型的字符串索引，有时会得到完全不同的Animal!
interface NotOkay {
    [x: number]: Animal;
    [x: string]: Dog;
}
```

字符串索引签名能够很好的描述`dictionary`模式，并且它们也会确保所有属性与其返回值类型相匹配。 因为字符串索引声明了 `obj.property`和`obj["property"]`两种形式都可以

```
interface NumberDictionary {
  [index: string]: number;
  length: number;    // 可以，length是number类型
  name: string       // 错误，`name`的类型与索引类型返回值的类型不匹配
}
```

###### 类类型

* 构造签名（new关键字）

  ```
  interface Point {
    new (x: number,y:number):Point
  }
  ```

* 构造类型函数

  * 包含一个或多个构造签名的对象类型被称为构造函数类型；

  * 构造函数类型可以使用构造函数类型字面量或包含构造签名的对象类型字面量来编写。

  * 形式如下：

    ```
    new <T1, T2, ...> (p1, p2, ...) => R
    
    等价
    
    { new <T1, T2, ...> (p1, p2, ...) : R }
    ```

  ```
  interface Point {
    x: number;
    y: number;
  }
  
  interface PointConstructor {
    new (x: number, y: number): Point;
  }
  
  class Point2D implements Point {
    readonly x: number;
    readonly y: number;
  
    constructor(x: number, y: number) {
      this.x = x;
      this.y = y;
    }
  }
  
  function newPoint(
    pointConstructor: PointConstructor,
    x: number,
    y: number
  ): Point {
    return new pointConstructor(x, y);
  }
  
  const point: Point = newPoint(Point2D, 2, 2);
  ```

* 继承接口

* 接口继承类

  当接口继承了一个类类型时，它会继承类的成员但不包括其实现。接口同样会继承到类的private和protected成员。 这意味着当你创建了一个接口继承了一个拥有私有或受保护的成员的类时，这个接口类型只能被这个类或其子类所实现（implement）

  ```
  class Control {
      private state: any;
  }
  interface SelectableControl extends Control {
      select(): void;
  }
  class Button extends Control implements SelectableControl {
      select() { }
  }
  class TextBox extends Control {
      select() { }
  }
  
  // 错误：“Image”类型缺少“state”属性。
  class Image implements SelectableControl {
      select() { }
  }
  class Location {
  
  }
  ```

#### 函数

###### 实例

```
// Named function
function add(x, y) {
    return x + y;
}

// Anonymous function
let myAdd = function(x, y) { return x + y; };
```

###### 可选参数和默认参数

###### 剩余参数

###### this

* this 和箭头函数
* this 参数
* 参数回调函数

###### 重载

#### 泛型

###### 概念

指定义函数、接口或类的时候，不预先指定类型，而是在使用的时候再指定类型的一种特性

```
function identity<T>(arg: T): T {
    return arg;
}

let output = identity<string>("myString");  // type of output will be 'string'

// 类型推论

let output = identity("myString");  // type of output will be 'string'
```

###### 泛型类型

* 泛型函数

  * 基本定义

    ```
    function identity<T>(arg: T): T {
        return arg;
    }
    
    let myIdentity: <T>(arg: T) => T = identity;
    ```

  * 调用签名定义

    ```
    function identity<T>(arg: T): T {
        return arg;
    }
    
    let myIdentity: {<T>(arg: T): T} = identity;
    ```

* 泛型接口

  ```
  interface GenericeFn {
    <T>(age: T): T
  }
  
  function identity<T>(arg: T): T{
    retutn arg
  }
  
  let myIdentity: GenericeFn = identity
  ```

* 泛型类

  ```
  class GenericNumber<T> {
      zeroValue: T;
      add: (x: T, y: T) => T;
  }
  
  let myGenericNumber = new GenericNumber<number>();
  myGenericNumber.zeroValue = 0;
  myGenericNumber.add = function(x, y) { return x + y; };
  ```

* 泛型约束

  ```
  function loggingIdentity<T>(arg: T): T {
      console.log(arg.length);  // Error: T doesn't have .length
      return arg;
  }
  
  interface Lengthwise {
      length: number;
  }
  
  function loggingIdentity<T extends Lengthwise>(arg: T): T {
      console.log(arg.length);  // Now we know it has a .length property, so no more error
      return arg;
  }
  ```

#### 枚举

* 数字枚举

  ```  
  enum E {
    A = 0;
    B
  }
  
  E['A'] === 0  E[0] === A
  E['B'] === 1  E[1] === B
  ```

* 字符串枚举

  ```
  enum E {
    UP ='UP',
    DOWN = 'DOWN'
  }
  ```

* 异构枚举 （字符数字混合）

  ```
  enum E {
    A = 0,
    B = 'B'
  }
  ```

* 计算和常量成员

  ```
  enum E {
   A = 0,
   B,
   C
  }
  
  A === 0 , B === 1, C === 2
  ```

* 联合枚举与枚举成员类型

* const 枚举

* 外部枚举

* 常数枚举

  ```
  常数枚举与普通枚举的区别是，它会在编译阶段被删除，并且不能包含计算成员。
  const enum Color {Red, Green, Blue = "blue".length}; // Error
  ```

#### 类型推论

#### 类型兼容性

TypeScript里的类型兼容性是基于结构子类型的.

```
interface Named {
    name: string;
}
class Person {
    name: string;
}

let p: Named;
// OK, because of structural typing
p = new Person();

尽管Person类没有明确说明其实现了Named接口。
```

###### 兼容性

* TypeScript结构化类型系统的基本规则是，如果`x`要兼容`y`，那么`y`至少具有与`x`相同的属性。比

  * 原始类型和对象类型（y 至少具有与 x相同的属性）

    ```
    interface Named {
        name: string;
    }
    
    let x: Named;
    // y's inferred type is { name: string; location: string; }
    let y = { name: 'Alice', location: 'Seattle' };
    x = y;
    
    这里要检查y是否能赋值给x，编译器检查x中的每个属性，看是否能在y中也找到对应属性
    ```

  * 函数 ( x 参数必须包含 y )

    ```typescript
    let y = (a: number) => 0;
    let x = (b: number, s: string) => 0;
    
    x = y; // OK
    y = x; // Error
    ```

    返回值 （y 必须包含 x)

    ```typescript
    let x = () => ({name: 'Alice'})
    let y = () => ({name: 'Alice', location: 'Seattle'})
    ```

* 枚举

  枚举类型与数字类型兼容，并且数字类型与枚举类型兼容。不同枚举类型之间是不兼容的。比如

  ```
  enum Status { Ready, Waiting };
  enum Color { Red, Blue, Green };
  
  let status = Status.Ready;
  status = Color.Green;  // Error
  ```

* 类

  比较两个类类型的对象时，只有实例的成员会被比较。 静态成员和构造函数不在比较的范围内。

  ```
  class Animal {
      feet: number;
      constructor(name: string, numFeet: number) { }
  }
  class Size {
      feet: number;
      constructor(numFeet: number) { }
  }
  let a: Animal;
  let s: Size;
  a = s;  // OK
  s = a;  // OK
  ```

  ###### 类的私有成员和受保护成员

  ```typescript
  类的私有成员和受保护成员会影响兼容性。 当检查类实例的兼容时，如果目标类型包含一个私有成员，那么源类型必须包含来自同一个类的这个私有成员。 同样地，这条规则也适用于包含受保护成员实例的类型检查。 这允许子类赋值给父类，但是不能赋值给其它有同样类型的类。
  ```

* 泛型

  * 因为TypeScript是结构性的类型系统，类型参数只影响使用其做为类型一部分的结果类型

    ```typescript
    interface Empty<T> {
    }
    let x: Empty<number>;
    let y: Empty<string>;
    
    x = y;  // OK, because y matches structure of x
    ```

    ```typescript
    interface Empty<T> {
      data: T
    }
    let x: Empty<number>;
    let y: Empty<string>;
    
    x = y;  // Error
    ```

#### 高级类型

###### 交叉类型

```typescript
function extend<T, U>(first: T, second: U): T & U {}

class Person { constructor(public name: string) { } }

interface Loggable { log(): void;}

class ConsoleLogger implements Loggable {}

var jim = extend(new Person("Jim"), new ConsoleLogger());
```

###### 联合类型

```typescript
function padLeft(value: string, padding: string | number) {
}

let indentedString = padLeft("Hello world", true); // errors during compilation
```

###### 类型保护与区分类型

```typescript
let pet = getSmallPet();

// 每一个成员访问都会报错
if (pet.swim) {
    pet.swim();
}
else if (pet.fly) {
    pet.fly();
}
```

断言保护

```typescript
let pet = getSmallPet();

if ((<Fish>pet).swim) {
    (<Fish>pet).swim();
}
else {
    (<Bird>pet).fly();
}
```

###### typeof类型保护

###### instanceof类型保护

###### 字符串字面量类型

* 字符串字面量类型允许你指定字符串必须的固定值。

```typescript
type Easing = "ease-in" | "ease-out" | "ease-in-out";
class UIElement {}

let button = new UIElement();
button.animate(0, 0, "ease-in");
button.animate(0, 0, "uneasy"); // error: "uneasy" is not allowed here
```

###### 数字字面量类型

```
function rollDie(): 1 | 2 | 3 | 4 | 5 | 6 {}
```

###### 完整性检查(never)

```typescript
function assertNever(x: never): never {
    throw new Error("Unexpected object: " + x);
}
function area(s: Shape) {
    switch (s.kind) {
        case "square": return s.size * s.size;
        case "rectangle": return s.height * s.width;
        case "circle": return Math.PI * s.radius ** 2;
        default: return assertNever(s); // error here if there are missing
    }
}
```

#### symbol

`symbol`类型的值是通过`Symbol`构造函数创建的。

```
let sym1 = Symbol();

let sym2 = Symbol("key"); // 可选的字符串key
```

#### 迭代器和生成器

###### 可迭代性

当一个对象实现了Symbol.iterator属性时，我们认为他是可迭代的。如：Map、Array、Set、String

* for ... of 

  ```
  用于遍历可迭代列表
  
  let someArray: string[] = ['one', 'two', 'three']
  
  for (let i of someArray) {
    console.log(i)             //  one   two   three          
  }
  ```

* for ... in

  ```
  迭代的是对象键值列表
  
  let someArray: string[] = ['one', 'two', 'three']
  
  for (let i in someArray) {
    console.log(i)           // 0  1  2
  }
  ```

  #### 命名空间

  “内部模块”现在称做“命名空间”。 “外部模块”现在则简称为“模块”。

  ###### 引入方式

  ```
  /// <reference path="Validation.ts" />
  ```

  ###### 编译输出

  ```
  tsc --outFile sample.js Test.ts
  ```

  ###### 解析默认引用 （先单个解析文件）

  ```
  <script src="Validation.js" type="text/javascript" />
  <script src="LettersOnlyValidator.js" type="text/javascript" />
  ```

  ###### 别名

  ```
  namespace Shapes {
      export namespace Polygons {
          export class Triangle { }
          export class Square { }
      }
  }
  
  import polygons = Shapes.Polygons;
  let sq = new polygons.Square(); // Same as "new Shapes.Polygons.Square()"
  ```

  ###### 外部命名空间

  ```
  declare namespace D3 {
      export interface Selectors {
          select: {
              (selector: string): Selection;
              (element: EventTarget): Selection;
          };
      }
  }
  
  declare var d3: D3.Base;
  ```

  ###### 命名空间和模块

  编译器首先尝试去查找相应路径下的`.ts`，`.tsx`再或者`.d.ts`。 如果这些文件都找不到，编译器会查找 *外部模块声明*。

* 不应该对模块使用命名空间，使用命名空间是为了提供逻辑分组和避免命名冲突

* 模块导出使用关键字export , 引用使用require

* 命名空间关键字 namespace , 引用使用 三斜杠 /// <reference path>

#### 装饰器

装饰器是一种特殊类型的声明，它能够被附加到类声明，方法， 访问符，属性或参数上。

* 类装饰器

  ```
  function Animal(name: string) {
    return function(target) {
      target.prototype.name =  name;
    }
  }
  @Animal('cat')
  class Cat {
    name: string;
    constructor(name: string){
      this.name = name
    }
  }
  ```

* 方法装饰器

  ```
  function method(target: any, propertyKey: string, descriptor: PropertyDescriptor) {
     console.log(target);
     console.log("prop " + propertyKey);
     console.log("desc " + JSON.stringify(descriptor) + "\n\n");
  };
  ​
  @addAge
  class Hello{
    name: string;
    age: number;
    constructor() {
      console.log('hello');
      this.name = 'yugo';
    }
  ​
    @method
    hello(){
      return 'instance method';
    }
  }
  ```

  关于解析器解析错误 (tsconfig.json   jsconfig.json)

  ```
  {
      "compilerOptions": {
          "target": "es6",
          "experimentalDecorators": true,
      }
  }
  ```

* 访问器装饰

  访问器装饰器应用于访问器的属性描述符，可用于观察，修改或替换访问者的定义。

  ```
  
  ```

* 方法参数装饰器

  参数装饰器表达式会在运行时当作函数被调用，可以使用参数装饰器为类的原型增加一些元素数据，传入三个参数。

  * 对于静态成员来说是类的构造函数，对于实例成员是类的原型对象

  * 参数的名字

  * 参数在函数列表中的索引

    ```
    function addParams () {
      return function(target: any, methodName: any, paramsIndex: any) {
        target.apiUrl = params;
      }
    }
    
    class Animal {
      constructor() {}
      getData(@addParams('id' id: any)) {
        console.log('我是getData方法')
      }
    }
    ```

    

* 属性装饰器