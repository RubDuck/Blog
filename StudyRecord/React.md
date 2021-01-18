## React

```react
import React from 'react';
import ReactDOM from 'react-dom';
```

#### 元素渲染

* 标签渲染

  ```
  const element = <h1> Hello world <h1>
  ReactDOM.render(
    element,
    document.getElementById('root')
  )
  ```

* 函数渲染

  ```react
  {/* JSX 创建类  render() 返回Dom节点 */}
  function Element (props) {
    return (
      <div>
            <h1> Hello world  { props.value }</h1>
      </div>
    )
  }
  ReactDOM.render(
    <Element  value={ 1 } />,
    document.getElementById('root')
  )
  ```

* React.Component 继承渲染 ( 实例 )

  ```react
  {/* JSX 创建类  render() 方法中 需要使用this.props 替代 props */}
  class Element extends React.component {
    render() {
      return (
        <div>
          <h1> Hello world { this.props.value }</h1>
        </div>
      )
    }
  }
  
  ReactDOM.render(
    <Element  value={ 1 } />,
    document.getElementById('root')
  )
  ```

#### JSX ( JS 语法扩展 )

* JavaScript 表达式 

  ```react
  {/* JSX 不能使用if else 语句 */}
  ReactDOM.render(
    <div>
      <h1> { i = 1 ? '正确' : '错误'} </h1> 
    </div>
    document.getElementById('root')
  )
  ```

* 样式

  ```react
  const size = { font-size: 12px }
  ReactDOM.render(
    <div>
      <h1 style={size}> 样式 </h1> 
    </div>
    document.getElementById('root')
  )
  ```

* 注释

  ```react
  {/* JSX 注释卸载花括号中 */}
  ReactDOM.render(
      <div>
      <h1>注释</h1>
      {/*注释...*/}
       </div>,
      document.getElementById('example')
  );
  ```

* 数组

  ```react
  {/* JSX 允许插入数组，数组会自动展开所有成员 */}
  const arr = [
    <h1>数组显示</h1>,
    <h2>数组渲染</h2>,
  ];
  ReactDOM.render(
    <div>{arr}</div>,
    document.getElementById('example')
  );
  ```

#### React 组件

* 函数组件

  ```react
  function Element () {
    return <h1> 函数组件 </h1>
  }
  ```

* 继承组件( 实例 )

  ```react
  class Element extends React.Component {
    render() {
      return <h1> 继承组件 </h1>
    }
  }
  ```

* 复合组件

  ```react
  function Title() {
    return (
      <h1> Title </h1>
    )
  }
  function Nav() {
    return (
      <h2> Nav </h2>
    )
  }
  function App () {
    return (
      <div>
        <Title />
        <Nav />
      </div>
    )
  }
  ReactDOM.render(
    <App />,
    document.getElementById('root')
  )
  ```

#### React State( 状态 )

* 实例

  ```react
  class Element extends React.Component {
    constructor(props) {
       super(props);
       this.state = { data: { name: '111' } }
    }
    render() {
      return (
        <div>
          <h1>State 状态</h1>
          <h2>状态值 { this.state.data.name }</h2>
        </div>
      )
    }
  }
  ReactDOM.render(
    <Element />,
    document.getElementById('root')
  )
  ```

* 生命周期 ( 挂载、卸载 )

  ```react
  { /* React 修改对象值 通过整体赋值修改 */ }
  class Element extends React.Component {
    constructor(props) {
       super(props);
       this.state = { data: { name: 111 } }
    }
    componentDidMount() {
      this.setvalue = setInterval(() => this.tick(), 2000)
    }
    componentWillUnmount() {
      clearInterval(this.setvalue)
    }
    tick() {
      let value = this.state.data.name;
      let result = Object.assign(this.state.data, { name: value + 1 });
      this.setState({
        data: result
      })
    }
    render() {
      return (
        <div>
          <h1>State 状态</h1>
          <h2>状态值 { this.state.data.name }</h2>
        </div>
      )
    }
  }
  ReactDOM.render(
    <Element />,
    document.getElementById('root')
  )
  ```

#### Prop

* 默认Props

  ```react
  class Element extends React.component{
    render() {
      return (
        <h1>默认props { this.props.name }</h1>
      )
    }
  }
  Element.defaultProps = {
    name: 'hcl'
  }
  ReactDOM.render(
    <Element />,
    document.getElementById('root')
  )
  ```

#### 事件处理

* 属性定义

  ```react
  class Element extends React.Component {
    constructor(props) {
      super(props);
      this.state = { name: '属性定义' };
      // 属性绑定
      this.handleClick = this.handleClick.bind(this)
    }
    handleClick() {
      console.log(this)
    }
    render() {
      return(
        <button onClick={ this.handleClick(e, args) }></button>
      )
    }
  } 
  ReactDOM.render(
    <Element />,
    document.getElementById('root')
  )
  ```

* es6箭头定义

  ```
  class Element extends React.Component {
    constructor(props) {
      super(props);
      this.state = { name: '属性定义' };
    }
    handleClick= ()=> {
      console.log(this)
    }
    render() {
      return(
        <button onClick={ this.handleClick(e, args) }></button>
      )
    }
  } 
  ReactDOM.render(
    <Element />,
    document.getElementById('root')
  )
  ```

* 传参方式

  ```react
  <button onClick = { this.handleClick.bind(this, id) }></button>
  <button onClick = { (e) => this.handleClick(id, e) }></button>
  ```

#### 条件渲染

* if  else

  ```react
  function Element(props) {
    return (
      <h1> if else </h1>
    )
  }
  function CreateElement (props) {
    const isShow = props.isShow;
    if (isShow) {
      return <Element />;
    } else {
      return null
    }
  }
  ReactDOM.render(
    <CreateElement isShow= { true }/>,
    document.getElementById('root')
  )
  ```

* 运算符 &&

  ```react
  function Element(props) {
    const isShow = props.isShow;
    return (
      <div>
        <h1> 运算符 && </h1>
        { isShow && <h2> 显示信息 </h2> }
      </div>
    )
  }
  ReactDOM.render(
    <CreateElement isShow= { true }/>,
    document.getElementById('root')
  )
  ```

#### 列表渲染

* 遍历数组

  ```react
  const numbers = [1, 2, 3, 4, 5]
  const elements = numbers.map(e => {
    return <li>{e}</li>
  })
  ReactDOM.render(
    <ul>{elements}</ul>,
    document.getElementById('root')
  )
  ```

* 唯一标志Key

  ```react
  function Li (props) {
    return <li>{ props.value }</li>
  }
  function Elements (props) {
    const numbers = props.numbers;
    const lisItems = numbers.map(e => {
      <Li key={ e.toString() } value={ e } />
    });
    return (
      <ul>
        { lisItems }
      </ul>
    )
  }
  
  const numbers = [1, 2, 3, 4]
  ReactDOM.render(
    <Elements numbers = { numbers } />,
    document.getElementById('root')
  )
  ```

* JSX 嵌入 map()

  ```react
  function LiItems(props) {
    const numbers = props.numbers;
    return (
      <ul>
        {numbers.map(e => <Li key={ e.toString() } value={ e } />)}
      </ul>
    )
  }
  ```

#### 组件API

* setState (设置状态)

  ```react
  class Element extends React.Component {
    constructor(props) {
      super(props);
      this.state = { number: 0 };
      this.handleClick = this.handleClick.bind(this)
    }
    handleClick() {
      this.setState(function(state) {
        return { number: state.number + 1 }
      });
    }
    render() {
      return (
        <h2 onClick={ this.handleClick.bind(this, args) }></h2>
        <h3 onClick={ (e) => this.handleClick(args, e) }></h3>
      )
    }
  }
  ```

* replaceState ( 替换状态 )

* setProps ( 设置属性 )

* replaceProps ( 替换属性 )

* forceUpdate ( 强制更新 )

* findDOMNode ( 获取DOM节点 )

* isMounted ( 判断组件挂载状态 )

#### 组件生命周期

###### 组件装载触发

* componentWillMount
* componentDidMount

###### 更新组件触发

* componentWillReceiveProps
* shouldComponentUpdate
* componentWillUpdate
* componentDidUpdate

###### 卸载组件触发

* componentWillUnmount

###### 执行顺序

* 创建时
  * constructor()
  * static getDerivedStateFromProps()
  * render()
  * componentDidMount()
* 更新时
  * static getDerivedStateFormProps()
  * shouldComponentUpdate()
  * render()
  * getSnapshotBeforeUpdate()
  * componentDidUpdate()
* 卸载时
  * componentWillUnmount()

#### DOM操作

* findDOMNode ( mounted之后 )

  ```react
  import { findDOMNode } from 'react-dom'
  componentDidMount() {
    const el = findDOMNode(this)
  }
  
  {/* findDOMNode() 不能用在无状态组件上 */}
  
  ```

* Refs

  ```react
  class Elements extends React.component{
    constructor(props) {
      super(props);
    }
    handleChange() {
      this.$refs.theInput.focus()
    }
    render() {
      return (
        <div>
          <input ref='theInput' />
        </div>
      )
    }
  }
  ```

#### 代码分割

* React.lazy

  ```react
  import React , { Suspense } from 'react';
  const OtherComponent = React.lazy(() => import('./OtherComponent'))
  
  function Element() {
    return (
      <div>
        <Suspense fallback={<div>Loading...</div>}>
          <OtherComponent />
        </Suspense>
      </div>
    )
  }
  
  {/* fallback 属性接任何在组件加载过程中你想展示的React元素 */}
  ```

* 异常捕获边界

  ```react
  import React, { Suspense } from 'react';
  import MyErrorBoundary from './MyErrorBoundary';
  
  const OtherComponent = React.lazy(() => import('./OtherComponent'));
  const AnotherComponent = React.lazy(() => import('./AnotherComponent'));
  
  const MyComponent = () => (
    <div>
      <MyErrorBoundary>
        <Suspense fallback={<div>Loading...</div>}>
          <section>
            <OtherComponent />
            <AnotherComponent />
          </section>
        </Suspense>
      </MyErrorBoundary>
    </div>
  );
  
  {/* 模块加载失败错误捕获 */}
  ```

* 路由代码分割

  ```react
  import React, { Suspense, lazy } from 'react';
  import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';
  
  const Home = lazy(() => import('./routes/Home'));
  const About = lazy(() => import('./routes/About'));
  const App = () => (
    <Router>
      <Suspense fallback={<div>Loading...</div>}>
        <Switch>
          <Route path='/' component={ Home } />
          <Route path='/about' component={ About } />
        </Switch>
      </Suspense>
    </Router>
  )
  
  {/* React.lazy     React Roter */}
  ```

#### Context

* 全局传递

  ```react
  const Context = React.crateContext('default');
  class App extends React.Component {
    render() {
      return(
        <Context.Provider value = 'dark'>
          <Toolbar />
        </Context>
      )
    }
  }
  
  function Toolbar() {
    return (
      <div>
       <ThemedButton />
      </div>
    )
  }
  
  class ThemedButton extends React.Component {
    static contextType = Context;
    render() {
      return <Button value={this.context} />
    }
  }
  ```

* createContext

  ```react
  const MyContext = React.createContext(defaultValue)
  
  {/* 全局变量创建 */}
  ```

* Context.Provider

  ```react
  <MyContext.Provider value={/* 传递值 */} />
  
  {/* Provider 定义传递父组件 */}
  ```

* Class.contextType

  ```react
  class Myclass extends React.Component {
    render() {
      let value = this.context;
      /* 基于 MyContext 组件的值进行渲染 */
    }
  }
  Myclass.contextType = MyContext;
  
  {/*
  挂载在class上的contextType属性会被重复制为一个有React.createContext()创建的Context对象。这能让你使用this.context来消费最近Context上的那个值 
  */}
  ```

#### 错误边界

* getDerivedStateFormError    componentDidCatch

  ```react
  {/* 当使用以上两个生命周期方法中的任意一个，那么它就变成一个错误边界 */}
  
  class ErrorBoundary extends React.Component {
    constructor(props) {
      super(props);
      this.state = { hasError: false }
    }
    static getDerivedStateFormError(error) {
      return { hasError: true }
    }
    render() {
      if (this.state.hasError) {
        return <h1>Something went wrong</h1>
      }
      return this.props.children
    }
  }
  ```

* try...catch 捕获事件处理器内部错误

  ```react
  class MyComponent extends React.Compnent{
    constructor(props) {
      super(props);
      this.state = { error: null };
      this.handleClick = this.handleClick.bind(this);
    }
    handleClick() {
      try {
        // 执行操作
      } catch (error) {
        this.setState({ error });
      }
    }
    render() {
      if (this.state.error) {
        return <h1>Caught an error.</h1>
      }
      return <button onClick={this.handleClick}>Click Me</button>
    }
  }
  ```

#### Fragments

* React.Fragment

  ```react
  {/* 允许将字列表分组，而无需向DOM添加额外节点 */}
  
  class Columns extends React.Component{
    render() {
      return (
        {/* 无需添加额外节点 */}
        <React.Fragment>
          <td>hello</td>
          <td>world</td>
        </React.Fragment>
      )
    }
  }
  ```

* 短语法

  ```react
  {/* 允许将字列表分组，而无需向DOM添加额外节点 */}
  
  class Columns extends React.Component{
    render() {
      return (
        {/* 无需添加额外节点 */}
        <>
          <td>hello</td>
          <td>world</td>
        <>
      )
    }
  }
  ```


#### HOC

高阶组件（HOC）是一个函数，函数接受组件和参数同时返回一个组件

```react
const Commit = withSubscription(
  CommentList,
  (DataSource) => DataSource.getComments()
)

{/* HOC函数 */}

function withSubscription(Component, selectData) {
  {/* 返回另一个组件 */}
  return class extends React.Component{
    constructor(props) {
      super(props);
      this.handleChange = this.handleChange.bind(this);
      this.state = {
        data: selectData(DataSource, props)
      }
    }
    componentDidMount() {
      {/* 负责订阅相关的操作 */}
      DataSource.addChangeListener(this.handleChange);
    }
    componentWillUnmount() {
      DataSource.removeChangeListener(this.handleChange);
    }
    handleChange() {
      this.setState({
        data: selectData(DataSource, this.props)
      })
    }
    render() {
      {/* 组件渲染 */}
      return <Component data={this.state.data} {...this.props} />
    }
  }
}
```

Note：不要改变原始组件

```react
{/* 不能修改原始组件 */}
function hocProps (Component) {
  Component.prototype.componentDidUpdate = function() {
    console.log('修改原始组件')
  }
  return Component
}
```

#### Hooks （在函数中调用react生命周期）

###### useState

修改state状态值

```react

import React , { useState } from 'react'

function StateFunction () {
  const [name, setName] = useState('函数')
  {/*    类名   修改函数名             初始值 */}
  return (
    <div onClick = { () => setName('我使用hooks变成这样了') }>
    </div>
  )
}
```

###### useEffect

又称为副作用Hooks，给没有生命周期的组件，添加结束渲染的信号。执行时机：在渲染结束之后执行

* 副作用Hooks ？
  * 副作用：数据获取，数据订阅，以及手动更改React组件中的DOM都属于副作用
  * 因为我们渲染出的页面都是静态的，任何在其之后的操作都会对他产生影响，所以称为副作用
* 使用
  * 第一个参数，接收一个函数作为参数
  * 第二个参数接受依赖列表
  * 返回一个函数，先执行返回函数，在执行参数函数

```react
import React, { useEffect, useState } from 'react'

function StateFunction () {
  const [num, setNum] = useState(0)
  
  useEffect(() => {
    console.log('222函数式组件渲染结束')
  }, [num])
  
  return(
    <div onClick={() => setNum( num => num + 1 )}></div>
  )
}
```

* 清除副作用

```react
const [positions,setPositions] = useState({ x:0,y:0 })
  
useEffect( () => {
    console.log('2222函数式组件结束渲染')

    const updateMouse = (e) => {
        console.log('打印当前位置')
        setPositions({ x:e.clientX, y:e.clientY })
    }
    document.addEventListener('click',updateMouse)
})
  
return (
    <div>
        <p>x:{ positions.x }</p>
        <p>y:{ positions.y }</p>
    </div>
)

{/*  以上会添加多个click监听事件  */}
```

```react
useEffect( () => {
    console.log('2222函数式组件结束渲染')

    const updateMouse = (e) => {
        console.log('渲染执行')
        setPositions({ x:e.clientX, y:e.clientY })
    }
    document.addEventListener('click',updateMouse)
  
    retutn ()=> {
     document.removeEventListener('click', updateMouse) 
     console.log('销毁---')
    }
})
```

###### useLayoutEffect

作用是在DOM更新完成之后执行莫格操作。执行时机：在DOM更新之后执行（执行顺序在useEffect之前）

```react
const [num, setNum] =useState(0)

{/* 在类组件中庸componentWillMount生命周期来实现 */}
useLayoutEffect(() => {
  console.log('useLayoutEffect')
  {/* 事件绑定 */}
  return () => {
    {/* 事件移除 */}
  }
})
```









