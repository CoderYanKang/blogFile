
---
layout: Redux
title: 初识Redux
date: 2018-05-09 11:19:47
categories: 
- React
tags: Redux
---

  说实话，懒了很久，虽然自己工作之余，也在零零碎碎的学习React相关的知识，不断的提高自己React Native ， React技术栈的能力，却也没有好好的总结反思。加上自己从事的是移动开发（Android 开发）。越是觉得有必要好好总结，巩固一下<!--more-->

  懒还话多！！！

###为什么需要Redux
多于大多数React应用，使用props , state足够处理简单的react app中组件直接的数据传递，配和context，我们可以解决大部分问题。

但是，当你的项目的业务逻辑复杂到一定的程度，组件太多，需要管理更多不断变化的state，前面的方法就会显得很痛苦。Redux的出现就是为了解决复杂应用中状态管理混乱的问题。

不过，对于简单的APP使用Redux是在无意义的增加开发复杂度。

###Redux重要组成部分

1、用户发出 Action操作




```
store.dispatch(action);
```



2、Store自动调用 Reducer方法。reducer方法（参数 state ，参数 action）




```
count(state , action){
    //根据不同action 返回不同的state
    return state;
}
```




3、State 发送改变后 ，Store 会触发监听函数



```
store.subscribe(listener);
```



4、listener 根据store得到改变后getState() ，state。重新render组件

###Action
本质上是普通的javascript对象，它是将数据从组件传递到store的信使，很像Android中的intent。
它必须有type字段数据来表示action的操作类型。



```
const GUN_ADD = 'add_gun';
{
    type: GUN_ADD,
    count: 10
}
```




###Reducer
action指定了我们的行为（修改更新指定的数据），确没有具体说明具体我们应该如何去修改更新数据。
Reducers就是用来指定根据不同的action，来修改state数据逻辑的方法。




```
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
```




###Store
将action和reducer联系起来。Redux应用只有一个单一的Store。可以有多个reducer.

维持应用的 state；

提供 getState() 方法获取 state，用于渲染组件；

提供 dispatch(action) 方法操作更新 state；

通过 subscribe(listener) 注册监听器;

通过 subscribe(listener) 返回的函数注销监听器

简单代码

app.js




```
import React, { Component } from 'react';
import './App.css';
class App extends Component
{
  render()
  {
        const store = this.props.store;
        return (
          <div className="App">

现在拥有我们拥有重机枪 {store.getState()} 把

              <button onClick={() => store.dispatch(this.props.addGun())}>捡到重机枪</button>
              <button onClick={() => store.dispatch(this.props.removeGun())}>丢掉重机枪</button>
          </div>
        );
  }
}
export default App;
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



index.js



```
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';
import {counter , addGun , removeGun } from './index.redux';
import {createStore} from 'redux';
let store = createStore(counter);
const render = () => ReactDOM.render(
    <App store = {store}  addGun = {addGun} removeGun = {removeGun}/>
    , document.getElementById('root'));
registerServiceWorker();
render();
//注册监听
store.subscribe(render);
```


总结
简单的使用了一下Redux , 介绍了Redux的一些基本概念。后续 Redux 中间件connect、Context传递store、以及封装更好的React-Redux的使用。