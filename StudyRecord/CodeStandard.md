

## 前端规范

#### HTML规范

* HTML双引号包裹属性值， js字符串采用单引号

* 标签闭合

* 有效操作（label for属性）

* 语义化标签

* 模块化注释

* 模块化

  * 每个模块必须有一个模块名
  * 每个模块的基本组成部分应该一致
  * 模块的子节点类目需带上模块名
  * 孙辈节点无需待模块名

  ```css
  <section class="m-detail">
      <header class="m-detail-hd">
          <h1 class="title">模块标题</h1>
      </header>
      <div class="m-detail-bd">
          <p class="info">一些实际内容</p>
      </div>
      <footer class="m-detail-ft">
          <a href="#" class="more">更多</a>
      </footer>
  </section>
  ```

#### CSS规范

* 文件引用

  ```
  一律使用link调用
  ```

* 命名-组成元素

  ```
  命名由单词、中划线或数组组成
  ```

* 分号和空格

* url引用资源使用双引号

  ````
  body{
    background-image: url("sprites.img")
  }
  ````

* 属性书写顺序

  ```
  盒子模型(display)
  定位(position、float、left、top、bottom、right、margin)
  尺寸(padding、width、height)
  基础属性(background、color、font-size、border)
  ```

  

#### CSS - in - JavaScript

* 使用驼峰命名

  ```css
  {
    bermudaTriangle: {
      display: none,
    }
  }
  ```

* 下划线修饰符

  ```css
  bermudaTriangle_theHulk: {
    color: 'green'
  }
  ```

  

#### Javascript

###### 引用

* const let 替代var

  ```
  const 防止修改
  const let 块级作用域
  ```

###### 对象

* 使用字面语法创建对象

  ```
  const item = {}   ||   const item = new Object() // bad
  ```

* 属性值缩写

  ```
  const obj = { lukeSkyWalker }  || const obj = { lukeSkyWalker: lukeSkyWalker } //bad
  ```

* 引号标注无效标志属性

  ```
  const obj = { foo: 3, 'data-blah': 3 }
  ```

* 不直接调用 hasOwnProperty、propertyIsEnumberable 和 isProtityeOf

  ```
  Object.prototype.hasOwnProperty.call(object, key)
  ```

* 使用扩展操作符，不适用Object.assign 浅拷贝一个对象

  ```
  const obj = {...obj}
  ```

###### 数组

* 使用字面语法创建数组

  ```
  const item = []   ||   const item = new Array() // bad
  ```

* 使用push取代直接赋值

* 使用...拷贝

* 数组转换 ( 使用 ... 替代 Array.from )

  ```
  const foo = document.querySelectorAll('.foo');
  const nodes = Array.from(foo);
  {/* best */}
  const nodes = [...foo]
  ```

* 使用 Array.from 替换 map

  ```
  const baz = [...foo].map(bar) // bad
  const baz = Array.from(foo, bar) // good
  ```

###### 解构

* 函数属性解构

  ```javascript
  {/* bad */}
  function getFullName(user) {
    const firstName = user.firstName;
    const lastName = user.lastName;
    return `${firstName} ${lastName}`;
  }
  
  {/* best */}
  function getFullName({ firstName, lastName }) {
    return `${firstName} ${lastName}`
  }
  ```

* 数组解构

  ```javascript
  const arr = [1, 2, 3, 4]
  {/* bad */}
  
  const first = arr[0]
  const second = arr[1]
  
  {/* good */}
  const [first, second] = arr
  ```

###### 字符

* 单引号
* 太长字符串 直接一行显示
* `` 模板链接字符串

###### 方法

* 使用命名函数表达式代替函数声明

  ```javascript
  {/* bad */}
  function foo() {}
  {/* good */}
  const short = function foo() {}
  ```

* 不要定义函数参数为 arguments 同时使用 ... 代替

  ```javascript
  {/* bad */}
  function concatenateAll() {
    const args = Array.prototype.slice.call
  }
  ```

  

#### GitLog

```react
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

* type 代表提交的类型，比如是修复一个bug还是增加一个新的feature。type类型如下：
* feat：新增feature
* fix：修复bug
* docs：仅仅修改了文档，比如README等等
* style：修改了空格、缩进等等，不改变代码逻辑
* refactor：代码重构，没有添加新功能或者修复bug
* perf：优化相关，比如提升性能、体验
* test：测试用例，包括单元测试、集成测试