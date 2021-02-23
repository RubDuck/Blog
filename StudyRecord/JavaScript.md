#### 词法作用域和动态作用域

* JavaScript属于词法作用域也称静态作用域，它的作用域在声明的时候就已经决定了。

```
var value = 1;

function foo() {
    console.log(value);
}

function bar() {
    var value = 2;
    foo();
}

bar();  // value = 1

var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f();
}
checkscope(); //  scope = 'local scope'
```

从上面的例子我们可以看到，JavaScript在声明函数的时候就已经决定了。

* 动态作用域语言（bash）将文件后缀名改为.bash

```
value=1
function foo () {
    echo $value;
}
function bar () {
    local value=2;
    foo;
}
bar  
```

#### 执行上下文栈

定义一个执行上下文栈

```
ECStack = [];
```

将全局执行上下文压入执行上下文栈的底部

```
ECStack = [
    globalContext
];
```

假设遇到以下代码，我们来用伪代码解释其执行过程

```
function fun3() {
    console.log('fun3')
}

function fun2() {
    fun3();
}

function fun1() {
    fun2();
}

fun1();
```

伪代码运行过程

```
// 伪代码

// fun1()
ECStack.push(<fun1> functionContext);

// fun1中竟然调用了fun2，还要创建fun2的执行上下文
ECStack.push(<fun2> functionContext);

// 擦，fun2还调用了fun3！
ECStack.push(<fun3> functionContext);

// fun3执行完毕
ECStack.pop();

// fun2执行完毕
ECStack.pop();

// fun1执行完毕
ECStack.pop();

// javascript接着执行下面的代码，但是ECStack底层永远有个globalContext
```

#### 变量对象

###### 执行上下文

* 变量对象（VO）
* 作用域链（Scope chain）
* this

###### 活动对象和变量对象

* 变量对象是规范上的或者说是引擎上实现的，不可以在JavaScript环境中访问，只有当进入一个执行上下文中。这个执行上下文的变量对象才会被激活
* 活动对象是在进入函数执行上下文时刻被创建。

###### 执行上下文执行过程

* 进入执行上下文
* 代码执行

#### 作用域链

当查找变量的时候，会先从当前上下文的变量对象中查找，如果没有找到，就会从父级的执行上下文的变量中查找，一直找到全局上下文的变量，也就是全局变量。由多个执行上下文的变量对象构成的链表就是作用域链。

函数执行上下文中作用域链和变量对象创建：

```
var scope = "global scope";
function checkscope(){
    var scope2 = 'local scope';
    return scope2;
}
checkscope();
```

执行过程如下：

* checkscope函数创建，同时将父级或者最顶层级保存到作用于链内部属性[[scope]]

  ```
  checkscope.[[scope]] = [
      globalContext.VO
  ];
  ```

* checkscope函数执行，同时创建checkscope函数执行上下文，然后将函数执行上下文推入上下文栈中

  ```
  ECStack = [
      checkscopeContext,
      globalContext
  ];
  ```

* checkscope函数不立即执行，开始准备工作，即创建执行上下文的一些属性：[[scope]]

  ```
  checkscopeContext = {
      Scope: checkscope.[[scope]],
  }
  ```

* 创建执行上下文属性: AO (形参、函数声明、变量声明)

  ```
  checkscopeContext = {
      AO: {
          arguments: {
              length: 0
          },
          scope2: undefined
      }，
      Scope: checkscope.[[scope]],
  }
  ```

* 创建只想上下文属性：将活动对象（AO）压入checkscope作用链顶端

  ```
  checkscopeContext = {
      AO: {
          arguments: {
              length: 0
          },
          scope2: undefined
      },
      Scope: [AO, [[Scope]]]
  }
  ```

* 执行函数，修改AO对象

  ```
  checkscopeContext = {
      AO: {
          arguments: {
              length: 0
          },
          scope2: 'local scope'
      },
      Scope: [AO, [[Scope]]]
  }
  ```

* 执行完毕，弹出执行栈

 ```
ECStack = [
    globalContext
];
 ```

过程：

* 函数创建 保存最上级作用域或父级作用域链
* 执行函数，创建函数执行上下文
* 执行上下文被压入上下文栈
* 添加函数上下文属性（复制函数属性创建作用域链）
* 添加函数上下文属性（创建VO对象，即加入形参、函数声明、变量声明）
* 添加函数上下文属性（将活动对象压入作用域链顶端）
* 上下文属性添加完成，修改AO对象
* 将函数上下文弹出执行栈

#### 闭包分析

```javascript
var scope = "global scope";
function checkscope(){
    var scope = "local scope";
    function f(){
        return scope;
    }
    return f;
}

var foo = checkscope();
foo();
```

执行过程

```javascript
1进入全局代码，创建全局执行上下文，全局执行上下文压入执行上下文栈
2全局执行上下文初始化
3执行 checkscope 函数，创建 checkscope 函数执行上下文，checkscope 执行上下文被压4入执行上下文栈
4checkscope 执行上下文初始化，创建变量对象、作用域链、this等
5checkscope 函数执行完毕，checkscope 执行上下文从执行上下文栈中弹出
6执行 f 函数，创建 f 函数执行上下文，f 执行上下文被压入执行上下文栈
7f 执行上下文初始化，创建变量对象、作用域链、this等
8f 函数执行完毕，f 函数上下文从执行上下文栈中弹出
```



#### 参数传递

* 按值传递

  ```
  var value = 1;
  function foo(v) {
      v = 2;
      console.log(v); //2
  }
  foo(value);
  console.log(value) // 1
  ```

  按值传递相当于拷贝了一份value值，函数内部的修改是不会影响原来的值

* 引用传递

  ```
  var obj = {
      value: 1
  };
  function foo(o) {
      o.value = 2;
      console.log(o.value); //2
  }
  foo(obj);
  console.log(obj.value) // 2
  ```

  引用传递就是，函数内部的改变会影响到该对象的值

* 共享传递

  共享传递是传递对象的引用副本

#### 创建对象的方式

###### 工厂模式

 工厂模式，即将创建对象封装在一个函数中，这样调用函数即可完成对象的创建

```
function createPerson(name) {
    var o = new Object();
    o.name = name;
    o.getName = function () {
        console.log(this.name);
    };

    return o;
}

var person1 = createPerson('kevin');
```

缺点：所有实例都只想一个原型

###### 构造函数模式

构造函数模式，即创建一个函数，函数内包含了一些属性和方法

```
function Person(name) {
    this.name = name;
    this.getName = function () {
        console.log(this.name);
    };
}

var person1 = new Person('kevin');
```

缺点：每次创建实例时每个方法都要被创建一次（在ES6中函数是一个对象，因此每次调用一次函数，等同创建一个新对象）

###### 原型模式

同构造函数，只不过将函数的属性和方法挂载到构造函数的原型上

```
function Person(name) {

}

Person.prototype.name = 'keivn';
Person.prototype.getName = function () {
    console.log(this.name);
};

var person1 = new Person();
```

缺点：所有的属性和方法都被共享，同时不能初始化参数

###### 组合模式

同时使用构造函数和原型模式

```
function Person(name) {
    this.name = name;
}

Person.prototype = {
    constructor: Person,
    getName: function () {
        console.log(this.name);
    }
};

var person1 = new Person();
```

###### 寄生构造函数模式

通过构造函数和内部对象

```
function Person(name) {

    var o = new Object();
    o.name = name;
    o.getName = function () {
        console.log(this.name);
    };

    return o;

}

var person1 = new Person('kevin');
console.log(person1 instanceof Person) // false
console.log(person1 instanceof Object)  // tru
```

这丫就是工厂函数的翻版，只不过调用采用了new的方式

#### 继承

###### 原型链继承

将父级属性赋值给对象的原型

```
function Parent () {
    this.name = 'kevin';
}

Parent.prototype.getName = function () {
    console.log(this.name);
}

function Child () {

}

Child.prototype = new Parent();

var child1 = new Child();

console.log(child1.getName()) // kevin
```

缺点：所有实例共享引用类型的属性

###### 构造函数继承

```
function Parent () {
    this.names = ['kevin', 'daisy'];
}

function Child () {
    Parent.call(this);
}

var child1 = new Child();

child1.names.push('yayu');

console.log(child1.names); // ["kevin", "daisy", "yayu"]

var child2 = new Child();

console.log(child2.names); // ["kevin", "daisy"]


function Parent () {
    this.names = ['kevin', 'daisy'];
}

function Child () {
    Parent.call(this);
}

var child1 = new Child();

child1.names.push('yayu');

console.log(child1.names); // ["kevin", "daisy", "yayu"]

var child2 = new Child();

console.log(child2.names); // ["kevin", "daisy"]
```

缺点：方法都在构造函数中定义，每次创建实例，方法都会被创建一遍。

###### 组合继承

采用构造继承和原型继承相结合的方式

```
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {

    Parent.call(this, name);
    
    this.age = age;

}

Child.prototype = new Parent();
Child.prototype.constructor = Child;

var child1 = new Child('kevin', '18');
```

缺点：构造函数实际上是被调用了两次

###### 寄生组合继承

为了解决组合继承中，构造函数被调用了两次的问题

```
function Parent (name) {
    this.name = name;
    this.colors = ['red', 'blue', 'green'];
}

Parent.prototype.getName = function () {
    console.log(this.name)
}

function Child (name, age) {
    Parent.call(this, name);
    this.age = age;
}

Child.prototype = new Parent();

var child1 = new Child('kevin', '18');

console.log(child1)
```

我们通过创建一个空的对象，同时将父级的原型方法赋值给空对象。最后通过创建这个对象来获得相对于的父级的方法。

#### Module

###### 运行时加载

```javascript
{/* CommonJS 模块 */}
let { stat, exists, readfile } = require('fs')
====
let _fs = require('fs')
let stat = _fs.stat
let exists = _fs.exists
let readfile = _fs.readfile
```

上面代码的实质是整体加载`fs`模块（即加载`fs`的所有方法），生成一个对象（`_fs`），然后再从这个对象上面读取 3 个方法。这种加载称为“运行时加载”，因为只有运行时才能得到这个对象，导致完全没办法在编译时做“静态优化”。

###### ES6 模块

```javascript
{/* ES6模块 */}
import { stat, exists, readFile } from 'fs'
```

上面代码的实质是从`fs`模块加载 3 个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即 ES6 可以在编译时就完成模块加载，效率要比 CommonJS 模块的加载方式高。当然，这也导致了没法引用 ES6 模块本身，因为它不是对象。

###### export

```javascript
{/* 写法一 */}
export var m = 1;
{/* 写法二 */}
var m = 1;
export { m }
{/* 写法三 */}
var n = 1;
export { n as m }

{/* function class 也需遵循 */}
export function f() {}

function f() {}
export { f }
```

###### 模块整体加载（*）

```
import * from 'fs'
```

#### Module加载实现

###### 加载规则

```javascript
{/* 浏览器加载ES6模块，也是用<script>标签，但是要加入type='module'属性 */}

<script type='module' src='./foo.js'></script>
```

###### 模块差异

* CommonJS 模块输出的是一个值的拷贝，ES6 模块输出的是值的引用。

  ```
  CommonJS 模块输出的是值的拷贝，也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。
  
  ES6 模块的运行机制与 CommonJS 不一样。JS 引擎对脚本静态分析的时候，遇到模块加载命令import，就会生成一个只读引用。等到脚本真正执行时，再根据这个只读引用，到被加载的那个模块里面去取值。
  ```

* 模块是运行时加载，ES6 模块是编译时输出接口。

  ```
  差异是因为 CommonJS 加载的是一个对象（即module.exports属性），该对象只有在脚本运行完才会生成。而 ES6 模块不是对象，它的对外接口只是一种静态定义，在代码静态解析阶段就会生成。
  ```

* 块的`require()`是同步加载模块，ES6 模块的`import`命令是异步加载，有一个独立的模块依赖的解析阶段。

#### Constructor

* vm.constructor的值指向的是它的父类

```javascript
let Cat;
let Dog;
{
  function Animal () {
    this.type = type;
  };
}
Animal.prototype.walk = function () { /*关于行走的代码*/ }
Cat = new Animal('cat');
Dog = new Animal('dog');
Animal.say = [] /* 添加属性无效 */
Cat.constructor.prototype.say = []; /* 添加属性有效 */
```





#### 关于this的指向

* 默认绑定（独立函数、默认声明、浏览器执行对象）
* 隐式绑定（XXX.fn()，即为调用对象方法的形式）
* 硬绑定（call、bind、applay，this为第一个参数）
* new绑定（即为创建的对象本生）

#### New模拟实现

我们知道，new的结果是一个新对象。所以我们在创建的时候，实际上是创建了一个新的对象，同时，将父级的方法挂载到新对象的原型上，最后返回这个对象。

```
function mineNew(){
   var obj = Object.create(null)
   Constructor = [].shift.call(arguments)
   obj._proto_ = Constructor.prototype
   return obj
}
```

这里之所以采用下面这种方式来创建对象，是因为这种方式创建的对象是没有任何的属性的。

```
Object.create(null)
```

综上所述：new做了什么？首先它创建了一个空的对象。然后，他挂在了父级的方法和属性。最后，返回了这个对象。

#### 字符串的查找方式

* indexOf()

  ```
  var str = 'hello world'
  str.indexOf('o') // 5
  ```

  indexOf返回字符串首次出现的位置，如果没有符合要求的字符，则返回-1

* match()

  ```
  var str = '123'
  var reg = RegExp(/3/g)
  str.match(reg)
  ```

  这是使用正在匹配的方式去判断

* search()

  ```
  var str = '123'
  var reg = RegExp(/3/g)
  str.search(reg)
  str.search("2")
  ```

  这个方法能够匹配字符串或者正则表达式，如果不存在则返回-1，存在则返回所在的位置

* test()

  ```
  var str = '123'
  var reg = RegExp(/3/g)
  str.test(reg)
  ```

  正则匹配，返回true或者false

* includes()

  ```
  var str = '123'
  str.includes(1) // true
  ```

  匹配是否存在字符，返回true false

#### 数组查找

* indexOf

  ```
  var a = [1,2,3,4,5]
  a.indexOf(1)
  ```

  返回存在的第一个位置

* find

  ```
  var a = [1,2,3,4,5,1]
  a.find(e=>e>2) // 3
  ```

  返回第一个符合结果的元素

* findIndex

  ```
  var a =[1,2,3,4,5]
  a.findIndex(e=>e>2) // 2 
  ```

  返回第一个符合元素的位置

#### 事件循环

###### 浏览器内核

* GUI 渲染线程
* JavaScript引擎线程
* 定时触发器线程
* 事件触发线程
* 异步http请求线程

###### 事件循环过程



#### 深拷贝

```javascript
function dep(obj,result = new Map()){
    if(result.get(obj)){      // 解决环引用
        return obj 
    }else{
        result.set(obj,true)
    }
    if(typeof obj =='object'){
        let res 
        obj instanceof Array?res=[]:res={}
        for(let i in obj){
            res[i] = typeof obj[i] == 'object'?dep(obj[i],result):obj[i]
        }
        return res


    }
    else{
        return obj
    }
}

var a = [1,2,3,{name:'xxl'}]
var b =dep(a)
a[3].name='ssl'

```

#### let const var

###### var

直接在内存预分配存储空间，等语句执行的时候，在存储对应的变量，如果是引用类型，那么会在堆内存里开辟一个内存空间存储实际内容。栈内存会存储指向堆内存的指针。

###### let const

let const 申明时时不会预分配内存空间的，并且会在分配变量时做一个检查，如果右相同的变量会报错。对于const来说，基础类型的值时不可以改变的，但是对于引用类型，可以改变堆内存中的值。

###### 变量提升和暂时性死区

* var 存在变量提升，let const 不存在变量提升。
* let const 变量声明之前，改变量都是不可用的，即为暂时性死区

#### 解构赋值

###### 数组解构

* 解构不成功，变量值等于undefined

  ````
  let [foo, [[bar], baz]] = [1, [[2]]];
  // foo：1   bar：2   baz：undefiend
  ````

* 不完全解构

  ````
  let [foo, [bar]] = [1, [2, 3]]
  // foo：1  bar：2
  ````

* 默认值

  ```
  let [foo = true] = []
  // foo：true
  ```

###### 对象解构

* 解构不成功，变量值等于undefined

  ```
  let { foo } = { bar: 'baz' }
  // foo：undefined
  ```

* 对象解构

  ```
  let { log, sin, cos } = Math
  ```

* 别名

  ```
  let { foo: baz } = { foo: 'aaa', bar: 'bbb' }
  // baz：'aaa'    foo： error: foo is not defined
  ```

* 默认值

  ```
  默认值生效条件是，对象的属性值严格等于undefined 
  
  var { x = 3 } = { x: undefined }
  // x：3
  
  var { x = 3 } = { x: null }
  // x：null
  ```

###### 字符串解构

* 字符串会被转为类数组的对象

  ```
  const [a, b, c, d] = 'hello'
  // a：'h'   b：'e'  c：'l'  d：'l'
  ```

###### 数值和布尔值得解构赋值

解构赋值时，如果等号右边是数值和布尔值，则会先转为对象

```
let { toString: s } = 123
// s: Number.prototype.toString

let { toString: s } = true
// s：Boolean.prototype.toString
```

undefined 和 null 无法转为对象，所以对他们进行解构赋值，都会报错

```
let { prop: x } = undefined // TypeError
let { prop: y } = null // TypeError
```

###### 用途

* 变量交换

  ```
  let x = 1;
  let y = 2;
  [x, y] = [y, x]
  ```

* JSON提取

  ```
  let jsonData = {
    id: 42,
    status: 'ok',
    data: [1, 2]
  }
  let { id, status, data:number } = jsonData
  // id：42  status：'ok'  number：[1, 2]
  ```

* 遍历Map

  ```
  const map = new Map();
  map.set('first', 'hello')
  map.set('second', 'world')
  for (let [key, value] of map) {
    console.log('key', key, 'value', value)
  }
  ```

#### 数据类型

###### typeof

###### instanceof

###### Object.prototype.toString.call()

###### getPrototypeOf

```
获取对象原型的方法
let a = []
Object.getPrototypeOf(a) // Number
```

###### constructor

```
let a = []
a.constructor.prototype // Number
```
#### 字符串新增方法

##### 实例方法：includes，startsWith，endsWith

* includes

  ```
  let s = 'hello world!'
  s.includes('o') //  true
  ```

* startsWith 返回布尔值，表示参数字符串是否在原字符串的头部。

  ```
  let s = 'hello world'
  s.startsWidth('hello') // true
  ```

* endsWith 返回布尔值，表示参数字符串是否在原字符串的尾部。

  ```
  let s = 'hello world'
  s.endsWith('world') // true
  ```

###### repeat

`repeat`方法返回一个新字符串，表示将原字符串重复`n`次。

```
'x'.repeat(2.9) // 'xx'
```

###### padStart  padEnd

* padStart 头部补全

  ```
  'x'.padStart(5, 'ad') // 'adadx'
  'xx'.padStart(3, 'ad') // 'axx'
  ```

* padEnd 尾部补全

  ```
  'x'.padEnd(5, 'ad') // 'xadad'
  'xx'.padEnd(5, 'ad') // 'xxada'
  ```

###### trimStart   trimEnd

* trimStart  消除字符串头部空格

  ```
  const a = ' abc '
  a.trimStart() // 'abc '
  ```

* trimEnd 消除字符串尾部空格

  ```
  const a = ' abc '
  a.trimEnd() // ' abc'
  ```

###### replaceAll

```
'abbbbbc'.replace('b', '_') // 'a_bbbbc'
'abbbbbc'.replaceAll('b', '_') // 'a_b'
```

#### 数值扩展

###### 数值判断

* isFinite  Number.isFinite()`用来检查一个数值是否为有限的（finite），即不是`Infinity

  ```
  Number.isFinite(15) true
  Number.isFinite(Infinity) false
  ```

* isNaN 用来检查数值是否为NaN

  ```
  Number.isNaN(NaN) // true
  ```

* isInteger 是否为整数

  ```
  Number.isInteger(1.00) // true
  Number.isInteger(1.1) // false
  ```

* isSafeInteger 是用来判断一个整数是否落在这个范围之内

  ```
  Number.MAX_SAFE_INTEGER === Math.pow(2, 53) - 1
  // true
  Number.MAX_SAFE_INTEGER === 9007199254740991
  // true
  
  Number.MIN_SAFE_INTEGER === -Number.MAX_SAFE_INTEGER
  // true
  Number.MIN_SAFE_INTEGER === -9007199254740991
  
  Number.isSafeInteger(1.1) // false
  Number.isSafeInteger(1) // true
  ```

* 最大值和精度

  ```
  javascript 所有数字都保存为64位浮点数，即
  最大精度：2^53
  最大值：2^1024
  ```

#### 数组扩展

###### 数组转换

* [...] 解构赋值

* Array.from

  ```
  let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
  }
  // es5
  var arr1 = [].slice.call(arrayLike)
  // es6
  var arr2 = Array.from(arrayLike)
  ```

* Array.of  将一组值转为数组

  ```
  Array.of(3, 11, 8) // [3, 11, 8]
  Array.of() // []
  ```

* copyWithin 成员位置复制（修改原数组）

  ```
  Array.prototype.copyWithin(target, start=0, end = this.length)
  [1, 2, 3, 4, 5].copyWithin(0, 3)
  // [4, 5, 3, 4, 5]
  
  上面代码表示将从 3 号位直到数组结束的成员（4 和 5），复制到从 0 号位开始的位置，结果覆盖了原来的 1 和 2。
  ```

* find  findIndex

  ```
  find 寻找符合条件的第一个数组成员
  findeIndex 寻找符合条件的第一个数组成员的位置
  ```

* fill

  ```
  填充数组
  ['a', 'b', 'c'].fill(7)  // [7,7,7]
  ['a', 'b', 'c'],fill(7, 0, 1) // [7, 'b', 'c']
  ```

* 遍历新方式

  * entries()

  * keys()

  * values()

    ```
    for (let index of ['a', 'b'].keys()) {
      console.log(index);
    }
    // 0
    // 1
    
    for (let elem of ['a', 'b'].values()) {
      console.log(elem);
    }
    // 'a'
    // 'b'
    
    for (let [index, elem] of ['a', 'b'].entries()) {
      console.log(index, elem);
    }
    // 0 "a"
    // 1 "b"
    ```

* 数组扁平化 flat  faltMap

  ```
  [1,2,[3]].flat()   // [1,2,3]
  [1,2,3,[[4]]].flat() // [1,2,3,[4]]
  [1,2,3,[[4]]].flat(2) // [1,2,3,4]    接受参数表示扁平深度
  
  
  // 相当于 [[2, 4], [3, 6], [4, 8]].flat()
  [2, 3, 4].flatMap((x) => [x, x * 2])
  // [2, 4, 3, 6, 4, 8]
  
  
  // 相当于 [[[2]], [[4]], [[6]], [[8]]].flat()
  [1, 2, 3, 4].flatMap(x => [[x * 2]])  只能展开一层
  // [[2], [4], [6], [8]]
  ```

#### 数据类型转化

###### 字符串

```
x.toString()
String(x);
'' + x;
```

###### 数组

```
Array.prototype.slice.call();
Array.from()
[...]
```

###### 数字

```
Number();
paseInt();
paseFloat();
temp - 0;
```



#### XMLHttpRequst

```javascript
let xhr = new XMLHttpRequset()
xhr.open(methods,url)
xhr.onreadystatechange = function(){
  if (xhr.readyStates == 4 && xhr.status == 200) {
    console.log('success')
  }
}
```

* 0 (未初始化)： (XMLHttpRequest)对象已经创建，但还没有调用open()方法；
* 1 (载入)：已经调用open() 方法，但尚未发送请求；
* 2 (载入完成)： 请求已经发送完成；
* 3 (交互)：可以接收到部分响应数据；
* 4 (完成)：已经接收到了全部数据，并且连接已经关闭。

#### Axios

###### Example

```
axios.get(url).then() // 
axios.post({
  method: '',
  url: '',
  data: {},
})
```

###### 请求配置

```
axios.create([config])

// 例子
const instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
});
```

###### 配置对象信息

```javascript
{
  url: '',
  method: '',
  baseURL: '',
  headers: {},
  timeout: '',
  withCredentials: '', // 跨域
  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data) {
    // 对 data 进行任意转换处理
    return data;
  }],
  // `paramsSerializer` 是一个负责 `params` 序列化的函数
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },
}
```

###### 拦截器

```javascript
// 添加请求拦截器
axios.interceptors.request.use(function (config) {
    // 在发送请求之前做些什么
    return config;
  }, function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  });

// 添加响应拦截器
axios.interceptors.response.use(function (response) {
    // 对响应数据做点什么
    return response;
  }, function (error) {
    // 对响应错误做点什么
    return Promise.reject(error);
  });
```

###### Axios全局请求过程

```
const config = {}; // 创建配置信息
const instance = axios.create(config); // 引入配置信息
instance.interceptors.response.use(responseSuccess, responseFail);// 引入拦截器

// 拦截处理
function responseSuccess(res) {};
function responseFail(res) {};
```

#### 垃圾回收机制

#### 内存泄漏

#### 事件侦听

#### Set 和 Map

###### 去除重复值

```
const s = new Set()
[1, 2, 3, 4, 1, 2].forEach(e => s.add(e))
```

###### 成员不转换 ( NaN除外 )

```javascript
const s = new Set()
s.add(5)
s.add('5')
s.size // 2

const t = new Set()
t.add(NaN)
t.add(NaN)
t.size // 1
```

###### 实例属性

* Set.prototype.constructor
* Set.prototype.size
* Set.prototype.add( value )
* Set.prototype.delete( value )
* Set.prototype.has( value )
* Set.prototype.clear( )

可遍历操作

* Set.prototype.keys( )
* Set.prototype.values( )
* Set.prototype.entries( )
* Set.prototype.forEach( )

###### WeakSet

* 成员只能为对象

  ```
  const ws = new WeakSet()
  ws.add(1) // Error
  ws.add(Symbol()) // Error
  ```

* 数组成员只能为对象

  ```
  const ws = new WeakSet()
  
  ws.add([[1, 2, 3]]) // right
  ws.add([1, 2]) // Error
  ```

* 实例属性

  * WeakSet.prototype.add(value)
  * WeakSet.prototype.delete(value)
  * WeakSet.prototype.has(value)

###### Map

* 实例属性

  * Map.size

  * Map.prototype.set( key,  value )
  * Map.prototype.get( key )
  * Map.prototype.has( key )
  * Map.prototype.delete( key )
  * Map.prototype.clear( )

* 遍历方法

  * Map.prototype.keys( )
  * Map.prototype.values( )
  * Map.prototype.entries( )
  * Map.prototype.forEach( )

#### Symbol

###### 属性唯一

```javascript
const a = Symbole('test')
const b = Symbole('test')

a == b // false
```

###### 设置对象属性并且获取值

```javascript
const a = Symbole('test')
const b = Symbole('test')
const object[a] = 'hello'
object[b] = 'world'
```

###### 属性名遍历

```javascript
const a = Symbole('test')
const b = Symbole('test')
let object = {}
object[a] = 'hello'
object[b] = 'world'

const objectSymbols = Object.getOwnPropertySymbols(object);

```

###### Symbol.for ( 同名属性 )

```
const a = Symbol.for('test')
const b = Symbol.for('test')

a === b  // true
```

###### Symbol.keyFor ( 获取Symbol )

```
const a = Symbol.for('test')
Symbol.keyFor(a)
```

###### Symbol.hasInstance

```javascript
/*
 对象 Symbol.hasInstance属性，指向一个内部方法。
 当其他对象使用instanceof运算符时，会调用该方法
*/

class Myclass {
  [Symbol.hasInstance](foo) {
    return foo instanceof Array;
  }
}

[1, 2, 3] instanceof new Myclass() // true

```

###### Symbol.match

```javascript
/*
  对象的Symbol.match指向一个函数，当执行str.match(object)时
  会调用该方法
*/

String.prototype.match(regexp)
// 等同于
regexp[Symbol.match](this)

class MyMatcher {
  [Symbol.match](string) {
    return 'hello world'.indexOf(string);
  }
}

'e'.match(new MyMatcher()) // 1
```

#### Proxy

proxy用于修改某些操作的默认行为，等同于再语言层面做出修改，所以属于一种 ‘元编程’

###### 基础属性

* get(target，propKey, receiver) ：拦截对象属性的读取
* set(target，propKey，value，receiver)：拦截对象属性的设置
* has(target，propKey)：拦截propKey in proxy
* deleteProperty(target，propKey)：拦截 delete proxy[propKey]
* ownKeys(target)
* getOwnPropertyDescriptor
* defineProperty(target, propKey,propDesc)：拦截Object.defineProperty
* preventExtensions
* getPrototypeOf
* isExtensible
* setPrototypeOf
* apply
* construct

###### get劫持

```javascript
const handle = {
  get(target, propKey, receiver) {
    return function() {
      console.log('-++++++')
      return target[propKey](...arguments)
    }
  }
}

const proxy = new Proxy(history, handle)
```



#### Promise

###### Promise.prototype.then

```
Promise.then(function(resolve) {}, function(reject) {})
```

###### Promise.prototype.catch

```javascript
{/* .then(null, rejection) || .then(undefiend, rejection) 别名 */}
```

###### Promise.prototype.finally

```
不论Promise的状态为resolve reject 都会执行改方法，回调函数不带参数
Promise
.then()
.finally((e) => {
  console.log(e)  // undefined
})
```

###### Promise.all

* Promise.all 返回值

  ```javascript
  const p = Promise.all([p1, p2, p3])
  
  {/*
    (1) 只有p1、p2、p3状态都为fulfilled => p为fulfilled || p1、p2、p3返回值组成数组、传递给p回调
    (2) 只要p1、p2、p3之中有一个被rejected => p为reject || p1、p2、p3返回值组成数组、传递给p回调
  */}
  ```

* catch错误处理

  作为参数的Promise实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch

  ```javascript
  const p1 = new Promise((resolve, reject) => {
    resolve('hello')
  })
  .then(result => result)
  .catch(e => e)
  
  const p2 = new Promise((resolve, reject) => {
    throw new Error('报错了')
  })
  .then(result => result)
  .catch(e => e);
  
  Promise.all([p1, p2])
  .then(result => console.log(result))
  .catch(e => console.log(e))
  
  {/* ['hello', Error: 报错了] */}
  ```

  如果Promise实例，没有自定义catch方法，就会调用Promise.all()的catch方法

  ````javascript
  const p1 = new Promise((resolve, reject) => {
    resolve('hello');
  })
  .then(result => result)
  
  const p2 = new Promise((resolve, reject) => {
    throw new Error('报错了')
  })
  .then(result => result)
  
  Promise.all([p1, p2])
  .then(result => console.log(result))
  .catch(e => console.log(e))
  
  {/* Error: 报错了 */}
  ````

###### Promise.reace

```javascript
const p = Promise.race([p1, p2, p3])
{/* 
  只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变
  那个率先改变的Promise实例的返回值，就传递给p的回调函数
*/}
```



###### Promise.allSettled

Promise.allSettled 方法接受一组Promise实例作为参数，只有等所有参数实例都返回结果，不管是fulfilled还是rejected，包装实例才会结束

```javascript
const resolved = Promise.resolve(42)
const rejected = Promise.reject(-1)

const allSettlePromise = Promise.allSettled([resolve, rejected])

allSettlePromise.then(function(e) {
  console.log(result)
})

// [ { status: 'fulfilled', value: 42 },{ status: 'reject', reason: -1 } ]

{/* filter 过滤 */}

const promises = [ fetch('index.html'), fetch('https://does-not-exist/') ];
const results = await Promise.allSettled(promises);

// 过滤出成功的请求
const successfulPromises = results.filter(p => p.status === 'fulfilled');

// 过滤出失败的请求，并输出原因
const errors = results
  .filter(p => p.status === 'rejected')
  .map(p => p.reason);
```



#### 生成器和迭代器

###### Iterator

* 概念分析

  ```javascript
  var it = makeIterator(['a', 'b'])
  
  function makeIterator(array) {
    var nextIndex = 0;    //保存变量值
    return {
      next: function() {
        return nextIndex < array.length ?
          { value: array[nextIndex++], done: false } :
          { value: undefiend, done: true }
      }
    }
  }
  
  it.next() // { value: 'a', done: false }
  it.next() // { value: 'b', done: false }
  it.next() // { value: undefiend, done: true }
  ```

  

###### Generator

* Iterator接口

  ```javascript
  const object = { name: 'xxl' };
  function* gen() {
    yield 1;
    yield 2;
    yield 3;
  }
  object[Symbol.iterator] = gen;
  
  ```

* next 方法参数

  ```javascript
  {/* next(value)   value 为当前yelid值 */}
  
  function* foo(x) {
    var y = 2 * (yield (x + 1));
    var z = yield(y / 3);
    return (x + y + z);
  }
  
  var a = foo(5);
  a.next() // { value: 6, done: false }
  a.next() // { value: NaN, done: false }
  a.next() // { value: NaN, done: true }
  
  var b = foo(5);
  b.next() // { value: 6, done: false }
  b.next(12) // { value: 8, done: false } // 将上次表达式的值设为12 即 y = 2 * 12
  b.next(13) // { value: 5 + 24 + 13  } // 将上次表达式的值设为13 即 z = 13
  ```

* for of 循环遍历

  ```
  function* gen() {
    yield 1;
    yield 2;
    yield 3;
  }
  for (var i of gen()) {
    console.log(i)
  }
  ```

* throw

  ```javascript
  {/* Generator 返回遍历器对象，都有一个throw方法，可以体内捕获错误 */}
  {/* throw方法抛出的错误要被内部捕获，前提是必须至少执行过一次next方法。 */}
  {/* 
  一旦 Generator 执行过程中抛出错误，且没有被内部捕获，就不会再执行下去了。如果此后还调用next方法，将返回一个value属性等于undefined、done属性等于true的对象，即 JavaScript 引擎认为这个 Generator 已经运行结束了。 
  */}
  var gen = function* () {
    try {
      yield 1;
    } catch (e) {
      console.log('内部捕获', e);
    }
  }
  
  var i = gen();
  i.next();   //  执行一次next方法
  try {
    i.throw('a');
    i.throw('b');
  } catch (e) {
    console.log('外部捕获', e)
  }
  
  // 内部捕获
  // 外部捕获
  ```

* return

  ```javascript
  {/* 返回结果值 并且终结遍历Generator 函数 */}
  {/* 
  一旦 Generator 执行过程中抛出错误，且没有被内部捕获，就不会再执行下去了
  如果Generator 函数 存在 try...finally 函数则执行finally 内容 
  */}
  
  function* numbers () {
    yield 1;
    try {
      yield 2;
      yield 3;
    } finally {
      yield 4;
      yield 5;
    }
    yield 6;
  }
  var g = numbers();
  g.next() // { value: 1, done: false }
  g.next() // { value: 2, done: false }
  g.return(7) // { value: 4, done: false }
  ...
  ```

* 对象属性的 Generator 函数

  ```
  let obj = {
    * myGenerator() {
       ...
    }
  }
  
  let obj = {
    myGenerator: function* () {}
  }
  ```

* Generator 函数的this 

  ```javascript
  {/* this指代Generator 实例 */}
  {/* new命令跟构造函数F一起使用，结果报错 */}
  
  function* g() {
    this.a = 11;
  }
  let obj = g();
  obj.a // undefiend
  ```

* co

  ```javascript
  function co (gen) {
    cosnt ctx = this;
    return new Promise(function(resolve, reject) {
      if (typeof gen === 'function') gen = gen.call(ctx);
      if (!gen || typeof gen.next !== 'function') return resolve(gen);
      onFulfilled()
      
      {/* onFulfilled */}
      
      funtion onFulfilled(res) {
        var ret;
        try {
          ret = gen.next(res);
        } catch (e) {
          return reject(e)
        }
        next(ret)
      }
      
      {/*  next  */}
      
      function next (ret) {
        if (ret.done) return resolve(ret.value);
        var value = resolve.call(ctx, ret.value);
        if (value) return value.then(onFulfilled, onRejected)
        return onRejected(
          new TypeError('promise  bad')
        )
      }
    })
  }
  ```

  

```javascript
function* gen() {
    let res1 = yield fetchData(url1);
    let res2 = yield fetchDaTA(URL2);
}

// GET RESULT ONE

let g = gen();
let result1 = g.next();
result1.value(function(data) {
    let result2 = g.next(data);
    result2.value(function(data) {
        g.next(data);
    })
})

// FET RESULT TWO

function run(gen){
    let g = gen();
    function next(data) {
        let result = g.next(data);
        if (result.done) { return; } 
        result.value(next);
    }
    next();
}
```



#### async/await

###### 基本用法（ N毫秒执行操作 ）

```javascript
function timeOut (ms) {
  return new Promise((resolve) => {
    setTimeout(resolve, ms)
  })
}

async function asyncPrint (value, ms) {
  await timeOut(ms);
  console.log(value)
}

asyncPrint('hello world', 2000)
```

###### 返回 Promise 对象

```javascript
{/* 
  async函数返回一个Promise对象  return 值为 Promise对象的resolve值 
  内部抛出错误，会导致返回的Promise对象变为 reject值
  async函数返回的Promise对象，必须等到内部所有的await命令后面的Promise对象执行完才会发生状态改变
  遇到retuen语句或者抛出错误会返回
*/}
```

###### 函数睡眠

```
function sleep(ms) {
  return new Promise(resolve => {
    setTimeout(resolve, ms)
  })
}

async function asyPrint(ms) {
  for(let i = 1; i< 10; i++) {
    console.log(i);
    await sleep(ms)
  }
}
```

###### 错误处理

```javascript
{/* await后面的异步操作出错，等同于async函数返回的Promise对象被reject */}

async function error() {
  await new Promise(function(resolve, reject) {
    throw new Error('出错了')
  })
}
error()
  .then(v => console.log(v))
  .catch(e => console.log(e))
```

###### 注意点

* await 命令放在 try...catch代码块中

  ```
  async function myFunction() {
    try {
      await somethingThatReturnsAPromise();
    } catch (err) {
      console.log(err)
    }
  }
  ```

* await 继发

  ```javascript
  let foo = await getFoo();
  let bar = await getBar();
  
  {/* 写法一 */}
  let [foo, bar] = await Promise.all([getFoo(), getBar()])
  
  {/* 写法二 */}
  let foo = getFoo();
  let bar = getBar();
  
  let foos = await foo;
  let bars = await bar;
  ```

* 循环方式

  ```javascript
  {/* 
    forEach()  方式不行
    for 循环允许
    reduce 循环允许
  */}
  ```

* 原理

  ```
  function spawn(genF) {
    return new Promise(function(resolve, reject) {
      const gen = genF()
      function step(nextF) {
        let next;
        try {
          next = nextF();
        } catch (e) {
          return reject(e);
        }
        if (next.done) return resolve(next.value);
        Promise.resolve(next.value).then(v => {
          step(gen.nextF(v))
        })
      }
    })
    step(gen.next(undefiend));
  }
  ```

#### Class

###### 静态方法

```javascript
{/* 方法前加上static关键字，改方法不会被实例继承，但是会被子类继承  */}

class Foo {
  static classMethod() {
    return 'hello'
  }
}
Foo.classMethod() // 'hello'
var foo = new Foo();
foo.classMethod() // Error


{/* 子类可继承 */}
class Bar extends Foo {
  
}
Bar.classMethod() // 'hello'
```

###### 静态属性

```javascript
{/* Class 本身的属性 */}
class Foo {
  
}
Foo.prop = 1;

{/* 静态新写法 static */}
class Foo {
  static prop = 1
}
```

###### 私有属性

* 命名区分

  ```javascript
  class Widget {
    // 公有方法
    foo () {}
    // 私有方法
    _bar () {}
  }
  ```

* symbol命名区分

  ```javascript
  const bar = Symbol('bar')
  export default class myClass{
    // 公有方法
    foo () {}
    // 私有方法
    [bar] () {}
  }
  ```

* 私有提案

  ```javascript
  {/* 私有提案在属性名前加：# */}
  class myClass {
    #count = 0;
    get () {}
    increment () {
      this.#count ++ ;
    }
  }
  ```

#### Class继承

###### Object.getPrototypeOf

```javascript
{/* 判断一个类是否继承另一个类 */}

Object.getPrototypeOf(ColorPoint) === Point
```

#### 函数柯里化

```javascript
function myAdd() {
  let sum = 0;
  for (let i =0; i < arguments.length; i++) {
    sum += arguments[i];
  }
  return sum;
}

function currying(fn, args) {
  let allArgs = args || [];
  return function() {
    let arg = allArgs.slice(0);
    for (let i = 0; i < arguments.length; i++) {
      arg.push(arguments[i]);
    }
    if (arg.length < 4) {
      return currying.call(this, fn, arg);
    } else {
      return fn.apply(this, arg);
    }
  }
}

var fn1 = currying(myAdd);
var res = fn1(1)(2)(3)(4);
console.log(res); // 10
```

函数柯里化，就目前理解而言，类似于参数的聚合。即当参数总和长度不超过限定值时，保存当前参数。

#### 浏览器模型

###### URL协议

```javascript
{/* URL支持javascript 协议 */}
<a href= "javascript：void 0"> 
```

###### DOMContentLoaded && load

```
DOMContentLoaded
　　当初始的 HTML 文档被完全加载和解析完成之后，DOMContentLoaded 事件被触发，而无需等待样式表、图像和子框架的完成加载。

load
　　load 仅用于检测一个完全加载的页面，页面的html、css、js、图片等资源都已经加载完之后才会触发 load 事件。
```

###### 浏览器组成

* 渲染引擎

  ```
  将网页代码渲染为用户视觉可以感知的平面文档，一些常见的渲染引擎
  Firefox：Gecko 引擎
  Safari: Webkit 引擎
  Chrome: Blink 引擎
  IE: Trident 引擎
  Edge: EdgeHTML 引擎
  ```

  * 解析代码：HTML代码解析为DOM，CSS代码解析为CSSDOM
  * 对象合成：将DOM和CSSDOM合成一颗渲染树
  * 布局：计算出渲染树的布局
  * 绘制：将渲染树绘制到屏幕

* 重流和重绘

  * 脚本操作和样式表操作触发重流
  * 重绘仅修改颜色等样式

* JavaScript 引擎

#### V8

###### 渲染引擎及网页渲染

```
浏览器当前功能发展包括：网络、资源管理、网页浏览、多页面管理、插件和扩展、书签管理、历史记录管理、
设置管理、下载管理、账户和同步、安全机制、隐私管理、外观主题、开发者工具等等。
```

###### 渲染引擎



#### BOM

###### window

```
window.closed // 关闭窗口
window.opener // 打开新窗口
window.devicePixelRatio  // DPR 比
```

* 属性

  * name
  * closed | opener
  * status
  * devicePixelRatio

* 方法

  * alert | prompt | confirm
  * open | close | stop
  * moveTo | moveBy
  * resizeTo | resizeBy
  * scrollTo | scroll | scrollBy
  * focus | blur
  * getSelection | getComputedStyle
  * requsetAnimationFrame

* 事件

  * load事件发生在文档在浏览器加载完毕，onload属性可以指定这个事件的回调函数

    ````react
    {/*
      load：页面完全加载完成触发，完全加载：不仅仅是dom结构加载完，还需要所有的链接引用都加载完
      ready: dom结构加载完成
    */}
    ````

* 事件监听

###### location

```javascript
const url = https://juejin.im/search?query=scrolltop

hash: # 
host: https://juejin.im/search:80
hostname: https://juejin.im/search
href: https://juejin.im/search?query=scrolltop
pathname: search
port: 80
protocol: https
search: ?query=scrolltop

localtion.reload(true) // 重新加载(true)强制刷新
```

###### navigator

```javascript
navigator.userAgent // 浏览器版本信息
navigator.plugins // 插件信息
navigator.platform // 平台信息(mac linx Win32)
navigator.onLine // 网络信息
navigator.language // 首选语言
```

###### history

```javascript
{/* 操作 */}
go() 
back()
forward()

pushState()
replaceState()
lenght

{/* 事件 */}
popstate // 触发条件(go、back、forward)
```

###### hash

```javascript
{/* 操作 */}
location.hash = '#xxx'

{/* 事件 */}
hashchange
```



###### cookie

强缓存 : Expries    Max-Age

```javascript
Expries  // 相对于本地的时间
Expires: Wed, 11 May 2018 07:20:00 GMT

Cache-Control // 绝对时间，优先级高于Expries
Cache-Control: max-age=315360000
```

协商缓存： Last-Modified   Etag

```
Last-Modified，If-Modified-Since // 文件最后的修改日期
ETag、If-None-Match // 根据文件的md5值进行修改判断
```

安全策略:  Secure  HttpOnly  SameSite

```java
Secure  // 指定浏览器只有在加密协议https 下才将cookie携带值服务器
HttpOnly // 指定当前无法通过脚本获取cookie值 即document.cookie 为null
SameSite // 限制第三方Cookie 请求携带。 含三种模式： Strict Lax None

Strict : 请求完全限制Cookie携带
Lax: 允许三种请求方式： 链接、预加载、Get请求
None: 关闭SameSite 模式，当前默认为Lax ,通过设置Secure 和 SameSite = None 关闭
```

###### Location

* 属性

  * location.href ( 整个url )
  * location.protocol ( 协议 )
  * location.host ( 主机 )
  * location.hostname ( 主机名 )
  * location.port ( 端口 )
  * location.pathname ( 路径 )
  * location.search ( 查询参数 )
  * location.hash ( hash )
  * location.username ( 域名用户名 )
  * location.password ( 域名前密码 )
  * location.origin ( 协议+主机名+端口 )

* 方法

  * location.assign ( 浏览器立刻跳转新的 URL，后退可以返回当前页面 )
  * location.replace ( 立刻跳转新的URL 替换当前history栈，后退无法返回当前页面 )
  * location.reload（ 刷新当前页面 ）

* URL解码和编码

  * 转码

    ```react
    encodeURI | encodeURIComponent
    
    {/* encodeURI 转码整个URL */}
    
    encodeURI('http://www.example.com/q=春节') // "http://www.example.com/q=%E6%98%A5%E8%8A%82"
    
    {/* encodeURIComponent 转码URL的组成部分，会转码除了语义字符之外所有字符，即元字符也会被转码 */}
    
    encodeURIComponent('春节')
    // "%E6%98%A5%E8%8A%82"
    encodeURIComponent('http://www.example.com/q=春节')
    // "http%3A%2F%2Fwww.example.com%2Fq%3D%E6%98%A5%E8%8A%82"
    ```

  * 解码

    ```javascript
    decodeURI | decodeURIComponent
    
    {/* decodeURI 解码整个URL */}
    
    decodeURI('http://www.example.com/q=%E6%98%A5%E8%8A%82') // "http://www.example.com/q=春节"
    
    {/* decodeURIComponent 解码URL的组成部分 */}
    
    decodeURIComponent('%E6%98%A5%E8%8A%82')  // "春节"
    ```

* URL接口

  * 构造函数

    ```javascript
    var url = new URL('http://www.example.com/index.html')
    url.href  //  'http://www.example.com/index.html'
    ```

  * 静态方法

    ```javascript
    {/* URL.createObjectURL 为上传/下载的文件、流媒体文件生成一个URL字符串 */}
    
    function handleFiles(files) {
      for (var i = 0; i < files.length; i++) {
        var img = document.createElement('img');
        img.src = window.URL.createObjectURL(files[i]);
        div.appendChild(img);
      }
    }
    
    {/* 结果 */}
    blob:http://localhost/c745ef73-ece9-46da-8f66-ebes574789b1
    
    {/* URL.revokeObjectURL  释放URL.createObjectURL对象 */}
    
    
    function handleFiles(files) {
      for (var i = 0; i < files.length; i++) {
        var img = document.createElement('img');
        img.src = window.URL.createObjectURL(files[i]);
        div.appendChild(img);
        img.onload = function() {
          window.URL.revokeObjectURL(this.src);
        }
      }
    }
    ```

    

#### XMLHttpRequest

###### 原生请求

```javascript
const xhr = new XMLHttpRequest()
xhr.onreadystatechange = function() {
  if (xhr.readyState === 4) {
    /* do something */
  }
}
xhr.send()
```

* response （返回请求体）

* responseType (数据类型)

  * “ ” ：text 文本数据
  * " arraybuffer "：ArrayBuffer对象
  * " blob "：Blob对象
  * " document "：Document 对象
  * " json "：JSON对象
  * " text "：字符串

* status ( HTTP状态码 )

* timeout （ 超时状态时间 ）| ontimeout （超时监听函数）

  ```javascript
  const xhr = new XMLHttpRequest()
  const url = '/server';
  xhr.ontimeout = function() {
    // 超时监听函数
  }
  
  ....
  xhr.timeout = 10 * 1000 // 超时时间设置
  ```

###### 事件监听

* onloadstart ( HTTP 请求发出 ) 的监听函数 ：loadstart
* onprogress ( 正在发送和加载数据 ) 的监听函数：progress
* onabort （ 请求终止 ）的监听函数：abort
* onerror ( 请求失败 ) 的监听函数：error
* onload ( 请求成功 ) 的监听函数：load
* ontimeout （ 用户超时请求 ）的监听函数：timeout
* onloadend （请求完成，不论成功或失败）的监听函数：loadend

文件上传 (xhr.upload)

```javascript
function unload(bolb) {
  const xhr = new XMLHttpRequset();
  xhr.open('POST', '/server', true);
  xhr.onload = function(e) {}
  
  // upload监听
  
  xhr.upload.onprogress = function(e) {
    if (e.lengthComputable) {
      console.log(e.loaded / e.total) * 100
    }
  }
  
  xhr.send(blob)
}

upload(new Blob(['hello world'], { type: 'text/plain' }))

```

###### 实例方法

* open (创建HTTP请求)

  ```
  xhr.open(method, url, async = false, user, password)
  ```

* send （发送请求）

  ```
  xhr.send(data)
  ```

* setRequsetHeader (设置头部信息)

  ```
  xhr.setRequsetHeader('Content-Type', 'application/json')
  xhr.setRequsetHeader('Content-Length', JSON.stringify(data).length)
  xhr.send(JSON.stringify(data))
  ```

* getResponseHeader ( 获取返回头部信息 )

  ```
  xhr.getResponseHeader('Last-Modified')
  xhr.getAllResponseHeaders()
  ```

###### 事件

* readyStateChange

* progress

  ```
  xhr.addEventListener('progress', callback)
  ```

* load | error | abort | loadend | timeout

#### 同源限制

###### 概述

同源指三个相同，协议相同、域名相同、端口相同

###### 跨域方式

* JSONP

  ```
  1.网页<script>元素，向服务器请求一个脚本，这不受同源限制（标签包含回调函数）
  2.服务器收到请求，拼接字符串，将JSON数据放在函数名里
  3.执行回调函数
  ```

* WebSocket

  ```
  应用层协议，首次连接依赖于http，首部添加Upgrade:websocket
  ```

* CORS

  ```
  跨域资源分享，属于跨源AJAX请求的根本解决方法
  ```

#### CORS通信

###### 两种请求

* 简单请求

  * HEAD
  * GET
  * POST

  ```
  头部字段
  
  Accept
  Accept-Language
  Content-Language
  Last-Event-ID
  Content-Type: 仅限于三个值 application/x-www-form-urlencoded、multipart/form-data、text/plain
  ```

###### 基本流程

* 对于简单请求，浏览器直接发出CORS请求，

  ```typescript
  具体来说在头信息之中增加一个Origin字段
  
  {/* 请求头信息 */}
  
  GET /cors HTTP/1.1
  Origin: *
  ```

* 服务器响应

  ```
  Access-Control-Allow-Origin: http://api.bob.com
  Access-Control-Allow-Credentials: true
  Access-Control-Expose-Headers: FooBar
  Content-Type: text/html; charset=utf-8
  ```

  * Access-Control-Allow-Origin (允许域名)
  * Access-Control-Allow-Credentials（是否允许发送Cookie）
  * Access-Control-Expose-Headers ()

###### withCredentials

* 关于浏览器请求Cookie携带

  ```
  当服务器指定：Access-Control-Allow-Credentials：true 时，即告知浏览器可以发送Cookie
  客户端：须设置属性 withCredentials: true // 当前浏览器默认为 true
  ```

###### 非简单请求

* 客服端 请求方式（PUT、DELETE)

  ```
  const url = 'http://api.alice.com/cors';
  const xhr = new XMLHttpRequest();
  xhr.open('PUT', url, true);
  xht.setRequestHeader('X-Custom-Header', 'value')
  xhr.sned()
  ```

* 服务端 - 预检

  ```
  服务端收到"预检请求"，检查Origin、Access-Control-Request-Method 和 Access-Control-Request-Headers
  ```

  * 回应

    ```
    HTTP/1.1 200 OK
    Date: Mon, 01 Dec 2008 01:15:39 GMT
    Server: Apache/2.0.61 (Unix)
    Access-Control-Allow-Origin: http://api.bob.com
    Access-Control-Allow-Methods: GET, POST, PUT
    Access-Control-Allow-Headers: X-Custom-Header
    ```

  * 否定回应

    ```
    OPTIONS http://api.bob.com HTTP/1.1
    Status: 200
    Access-Control-Allow-Origin: https://notyourdomain.com
    Access-Control-Allow-Method: POST
    ```

  * 正常回应

    ```
    PUT /cors HTTP/1.1
    Origin: http://api.bob.com
    Host: api.alice.com
    X-Custom-Header: value
    Accept-Language: en-US
    Connection: keep-alive
    User-Agent: Mozilla/5.0...
    ```

    

  

#### Axios

默认配置

```js
export default {
  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL,

  // `headers` 是即将被发送的自定义请求头
  headers: {
    common: {
      'X-Requested-With': 'XMLHttpRequest',
    },
  },

  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [
    (data) => {
      if (isObject(data) || isArray(data)) {
        return stringify(data, { arrayFormat: 'repeat' });
      }
      return data;
    },
  ],

  // `paramsSerializer` 是一个负责 `params` 序列化的函数
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer(params) {
    return stringify(params, { arrayFormat: 'repeat' });
  },

  // `withCredentials` 表示跨域请求时是否需要使用凭证(cookie)
  withCredentials: true,

  // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  timeout: Number(process.env.VUE_APP_TIMEOUT || 3000),

  // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json',

  // `xsrfCookieName` 是用作 xsrf token 的值的cookie的名称
  xsrfCookieName: 'csrftoken',

  // `xsrfHeaderName` 是承载 xsrf token 的值的 HTTP 头的名称
  xsrfHeaderName: 'X-CSRFToken',
};

```

拦截器

```
const instance = axios.create(defaults);
instance.interceptors.response.use(responseSuccess, responseFail);
```

#### JWT

Json Web Token: 包含三部分

###### header

```javascript
指定了该 JWT 使用的签名算法:

header = '{"alg":"HS256","typ":"JWT"}'// `HS256` 表示使用了 HMAC-SHA256 来生成签名。
```

###### playload

```javascript
表明了 JWT 的意图：

payload = '{"loggedInAs":"admin","iat":1422779638}' //iat 表示令牌生成的时间
```

###### signature

1. 输入 `base64url` 编码的 header 部分、 `.` 、`base64url` 编码的 playload 部分，输出 unsignedToken。
2. 输入服务器端私钥、unsignedToken，输出 signature 签名。

```javascript
const base64Header = encodeBase64(header)
const base64Payload = encodeBase64(payload)
const unsignedToken = `${base64Header}.${base64Payload}`
const key = '服务器私钥'

signature = HMAC(key, unsignedToken)
```

计算结果

```javascript
const base64Header = encodeBase64(header)
const base64Payload = encodeBase64(payload)
const base64Signature = encodeBase64(signature)

token = `${base64Header}.${base64Payload}.${base64Signature}`
```

服务器判断

```javascript
const [base64Header, base64Payload, base64Signature] = token.split('.')

const signature1 = decodeBase64(base64Signature)
const unsignedToken = `${base64Header}.${base64Payload}`
const signature2 = HMAC('服务器私钥', unsignedToken)

if(signature1 === signature2) {
  return '签名验证成功，token 没有被篡改'
}

const payload =  decodeBase64(base64Payload)
if(new Date() - payload.iat < 'token 有效期'){
  return 'token 有效'
}
```

