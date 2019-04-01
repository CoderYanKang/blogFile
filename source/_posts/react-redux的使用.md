---
layout: Redux
title: React-Redux的使用
date: 2018-06-10 11:19:47
categories: 
- React
tags: React-Redux
---

  在实际的开发过程中，由于一个App通常比较复杂。我们不能在每个需要使用Store的地方，都通过import文件导入。一方面，加大维护难度，另一方面，可能我们根本不知道Store文件的绝对位置。<!--more-->

   对于这种情况，我们应该在最顶层的父组件通过props依次传递下去。作为Store引入的唯一入口，但这样的缺陷时，所有的子组件都需要传递，即使你根本没有使用它，你也需要将它传递下去，对于开发者这是很不友好的。

   React的Context可以帮助我们完美解决这个问题
所谓Context，就是上下文环境，和Android的Context很像，可以让整个组件树都可以访问的一个公共对象。

  首先，父组件需要配置Context，提供Context支持，其次，子组件说明自己需要Context，可以通过this.context访问这个公共的环境对象。

传递例图



###使用Context传递store
首先，创建一个叫Provider的父组件，用户提供store传递的容器组件。



```
import PropTypes from 'prop-types';
import React,{Component} from 'react';
export default class Provider extends Component{
    //支持Context
    getChildContext() {
        return {
          store: this.props.store
        };
    }
    //只渲染子组件
    render() {
        return this.props.children;
    }
}
//让Provicer 能够被React认可为一个Context提供者
Provider.childContextTypes = {
  store:  PropTypes.object
};
```



index.js




```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';
import {counter , addGun , removeGun } from './index.redux';
import {createStore , applyMiddleware} from 'redux';
import MyMiddleware from './MyMiddleware';
import Provider from './Provider';
// let store = createStore(counter , applyMiddleware(MyMiddleware));
let store = createStore(counter);
const render = () => ReactDOM.render(
    <Provider store = {store}>
        <App addGun = {addGun} removeGun = {removeGun}/>
    </Provider>
    , document.getElementById('root'));
registerServiceWorker();
render();
//注册监听
store.subscribe(render);
```



app.js




```
import React, { Component } from 'react';
import PropTypes from 'prop-types';
import './App.css';
class App extends Component
{
  render()
  {
        //使用this.context访问上下文对象
        const store = this.context.store;
        return (
          <div className="App">
              <p>现在拥有我们拥有重机枪 {store.getState()} 把</p>
              <button onClick={() => store.dispatch(this.props.addGun())}>捡到重机枪</button>
              <button onClick={() => store.dispatch(this.props.removeGun())}>丢掉重机枪</button>
          </div>
        );
  }
}
//使用Context需要知道Context类型
App.contextTypes = {
    store: PropTypes.object
};
export default App;
```



如果我们自定义constructor方法，需要将contxt传入参数，不然无法正常使用context


```
constructor(props , context){
    super(props , context);
    ...
}
```


因为Context，我们不需要反复传递store ，只需要在store入口添加context支持方法及context类型。在需要使用的地方知道context类型，使用thi.context调用即可。

不过官方提示我们不要滥用Context，除非你真的需要

###react-redux的出现

react-redux，将这块逻辑抽离出来了，有了react-redux我们可以直接在容器组件中传递store即可。

###react-redux的构成

Provider组件
为容器组件提供store对象，来生成UI组件参数。不用一层一层去传递store。

connect()
react-redux提供connect方法，用于生成容器组件。

容器组件，负责数据逻辑和处理 ，不负责UI的渲染，又叫聪明组件。
傻瓜组件，只负责UI的渲染，将容器组件提供的数据渲染出来。

app.js



```
import React, { Component } from 'react';
import './App.css';
import { connect }from 'react-redux';
import {addGun , removeGun } from './index.redux';
class App extends Component
{
    render()
    {
        return (
            <div className="App">
                <p>现在拥有我们拥有重机枪 {this.props.num} 把</p>
                <button onClick={this.props.addGun}>捡到重机枪</button>
                <button onClick={this.props.removeGun}>丢掉重机枪</button>
            </div>
        );
    }
}
//传递的state数据
function mapStateToProps(state)
{
    return {num: state};
}
//返回 ActionCreater
const actionsCreater = {addGun , removeGun};
//包裹容器组件
export default connect(mapStateToProps , actionsCreater)(App);
```



index.js



```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';
import {counter} from './index.redux';
import {createStore , applyMiddleware} from 'redux';
import MyMiddleware from './MyMiddleware';
// import Provider from './Provider';
// let store = createStore(counter , applyMiddleware(MyMiddleware));
import {Provider} from 'react-redux';
// import Provider from "./Provider";
let store = createStore(counter);
const render = () => ReactDOM.render(
    <Provider store = {store}>
        <App />
    </Provider>
    , document.getElementById('root'));
registerServiceWorker();
render();
//注册监听
store.subscribe(render);
```



index.redux.js



```
//action 类型
const ADD_GUN = 'add_gun';
const REMOVE_GUN = 'remove_gun';
export function counter(state = 0 , action)
{
    if(action)
        switch (action.type)
        {
            case ADD_GUN:
                return state + 1;
            case REMOVE_GUN:
                return state - 1 >= 0 ? state - 1 : 0;
            default:
                return 10;
        }
}
export function addGun()
{
    return {type : ADD_GUN};
}
export function removeGun()
{
    return {type : REMOVE_GUN};
}
```



使用装饰品配置connect
安装装饰器库



```
npm install babel-plugin-transform-decorators-legacy –save-dev
```

安装babel-plugin-import


```
npm install babel-plugin-import –save-dev
```


安装react-app-rewired


```
npm react-app-rewired –save-dev
```

在项目根目录下配置 config-overrides.js



```
const { injectBabelPlugin } = require('react-app-rewired');
module.exports = function override(config, env)
{
    config = injectBabelPlugin(["transform-decorators-legacy"], config);
    return config;
};
```


package.json


```
{
  "name": "boss_app",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "babel-preset-env": "^1.7.0",
    "react": "^16.4.0",
    "react-dom": "^16.4.0",
    "react-redux": "^5.0.7",
    "react-scripts": "1.1.4",
    "redux": "^4.0.0",
    "redux-thunk": "^2.3.0"
  },
  "scripts": {
    "start": "react-app-rewired start",
    "build": "react-app-rewired build",
    "test": "react-app-rewired test --env=jsdom",
    "eject": "react-scripts eject"
  },
  "devDependencies": {
    "babel-plugin-import": "^1.2.0",
    "react-app-rewired": "^1.2.9",
    "babel-plugin-transform-decorators-legacy": "^1.3.4"
  }
}
```


也可eject后再配置，这里没有通过eject实现。

另一种使用

app.js




```
import React, { Component } from 'react';
import './App.css';
import { connect }from 'react-redux';
import {addGun , removeGun } from './index.redux';
//传递的state数据
function mapStateToProps(state)
{
    return {num: state};
}
//返回 ActionCreater
const actionCreater = {addGun , removeGun};
//包裹容器组件
@connect(mapStateToProps , actionCreater)
class App extends Component
{
    render()
    {
        return (
            <div className="App">
                <p>现在拥有我们拥有重机枪 {this.props.num} 把</p>
                <button onClick={this.props.addGun}>捡到重机枪</button>
                <button onClick={this.props.removeGun}>丢掉重机枪</button>
            </div>
        );
    }
}
export default App;
```


