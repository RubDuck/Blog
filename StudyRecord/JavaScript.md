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

`````
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

* 基本用法（ N毫秒执行操作 ）

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

* 返回 Promise 对象

  ```javascript
  {/* 
    async函数返回一个Promise对象  return 值为 Promise对象的resolve值 
    内部抛出错误，会导致返回的Promise对象变为 reject值
    async函数返回的Promise对象，必须等到内部所有的await命令后面的Promise对象执行完才会发生状态改变
    遇到retuen语句或者抛出错误会返回
  */}
  ```

* 函数睡眠

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

* 错误处理

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

* 注意点

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

#### BOM

###### window

```
window.closed // 关闭窗口
window.opener // 打开新窗口
window.devicePixelRatio  // DPR 比
```

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

```
go() 
back()
forward()
lenght
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

