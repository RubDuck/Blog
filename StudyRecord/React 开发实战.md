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

  



