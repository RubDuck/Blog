## React 开发实战

#### Styled-components

* 安装

  ````
  npm install styled-components --save
  ````

* 创建 style.js

  ```react
  {/* 创建 style.js */}
  
  import { createGlobalStyle } from'styled-components';
  
  export const GlobalStyle = createGlobalStyle`
  	body {
  		line-height: 1;
  	}
  	html, body {
  		background: #f2f3f4;;
  	}
  	ol, ul {
  		list-style: none;
  	}
  	blockquote, q {
  		quotes: none;
  	}
  	blockquote:before, blockquote:after,
  	q:before, q:after {
  		content: '';
  		content: none;
  	}
  	table {
  		border-collapse: collapse;
  		border-spacing: 0;
  	}
  	a {
  		text-decoration: none;
  		color: #fff;
  	}
  `
  ```

  ```react
  {/* App.js 引入 */}
  
  import React from 'react';
  import { GlobalStyle } from  './style';
  
  function App () {
    return (
      <div>
        <GlobalStyle></GlobalStyle>
      </div>
    )
  }
  
  export default App;
  ```

#### 组件

* 样式 ( style.js )

  ```react
  import styled from'styled-components';
  import style from '../../assets/global-style';
  
  export const Top = styled.div`
    display: flex;
    flex-direction: row;
    justify-content: space-between;
    padding: 5px 10px;
    background: ${style ["theme-color"]};
    &>span {
      line-height: 40px;
      color: #f1f1f1;
      font-size: 20px;
      &.iconfont {
        font-size: 25px;
      }
    }
  `
  ```

* 组件

  ```react
  import React from 'react';
  import { Top } from 'style.js'
  
  function Home(props) {
    return(
      <Top>Home</Top>
    )
  }
  
  export default React.memo(Home)
  ```

* React.memo ( 高阶组件 )

  ```react
  /*
  如果你的组件在相同 props 的情况下渲染相同的结果，那么你可以通过将其包装在 React.memo 中调用，以此通过记忆组件渲染结果的方式来提高组件的性能表现。这意味着在这种情况下，React 将跳过渲染组件的操作并直接复用最近一次渲染的结果
  React.memo 仅检查 props 变更。如果函数组件被 React.memo 包裹，且其实现中拥有 useState 或 useContext 的 Hook，当 context 发生变化时，它仍会重新渲染。
  */
  ```

  * 默认情况下其只会对复杂对象做浅层对比，如果你想要控制对比过程，那么请将自定义的比较函数通过第二个参数传入来实现

    ```react
    function MyComponent(props) {
     {/* 使用 props 渲染 */}
    }
    function areEqual(prevProps, nextProps) {
      {/* 如果nextProps方法返回结果和prevProps结果一致则返回 true */}
    }
    export default React.memo(MyComponent, areEqual)
    ```

    

#### Router

* 安装

  ```
  npm install react-router react-router-dom react-router-config --save
  ```

* 创建 routers

  ```react
  {/* router.js */}
  
  import React from 'react';
  import { Redirect } from "react-router-dom";
  import Home from '../application/Home';
  import Recommend from '../application/Recommend';
  
  export default [
    {
      path: "/",
      component: Home,
      routes: [
        {
          path: "/",
          exact: true,
          render: () => (
            <Redirect to={"/recommend"}/>
          )
        },
        {
          path: "/recommend",
          component: Recommend
        },
      ]
    }
  ]
  ```

* 使用

  ```react
  import React from 'react';
  import { renderRoutes } from 'react-router-config';//renderRoutes 读取路由配置转化为 Route 标签
  import routes from './routes/index.js';
  import { HashRouter } from 'react-router-dom';
  import { GlobalStyle } from  './style';
  
  function App () {
    return (
      <HashRouter>
        <GlobalStyle></GlobalStyle>
        {/* 多层路由引入 */}
        { renderRoutes (routes) }
      </HashRouter>
    )
  }
  
  export default App;
  ```

#### Redux

* 安装

  ```
  npm install redux redux-thunk redux-immutable react-redux immutable --save
  ```

* 创建 store

  ```react
  import { createStore, compose, applyMiddleware } from 'redux'
  import thunk from 'redux-thunk'
  import reducer from './reducer'
  const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ || compose;
  
  const store = createStore (reducer, composeEnhancers (
    applyMiddleware (thunk)
  ));
  
  export default store;
  ```

* 注入

  ```react
  import React form 'react'
  import { Provider } from 'react-redux'
  import store from './store/index'
  
  function App() {
    return (
      <Provider store={store}>
        
      </Provider>
    )
  }
  
  export default App
  ```


#### Better-scroll

###### forwardRef

```react
{/* 
  Reacr.forwardRef会创建一个React组件，这个组件能够将其接受的ref属性转化到其他组件树下的另一个组件中。 
*/}

const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className='FancyButton'>
    {props.children}
  </button>
))

const ref = React.createRef();
<FancyButton ref={ref}> click me! </FancyButton>
```

#### Hook

###### 函数组件定义

```
function Header(props) {
  return (
    <div>这是一个函数组件</div>
  )
}
export default Header
```

###### 引入useState

```
import React, {useState} from "React"; //解构引入
function Header(props) {
  const [name, setName] = useState("张三") //name的初始值是“张三”，使用setName("李四")可以进行修改
  return (
    <div onClick={()=>{setName(“李四”)}}>{ name }</div> 
  )
}
export default Header
```



