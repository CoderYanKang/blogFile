---
layout: Redux
title: Redux中间件
date: 2018-06-05 11:19:47
categories: 
- React
tags: Redux
---


初次接触到中间件是在Connect和Express中，给我的直观印象是很像okhttp的拦截器。<!--more-->

Redux中间件是什么
Redux中间件依赖applyMiddleware函数，通过对store.dispatch方法进行改造，在发出action行为和执行Reducer执行之间，来添加一些扩展的功能，比如增加日志打印，异步处理，错误捕获等功能。

模拟添加日志打印中间件

```
let next = store.dispatch;
//改造dispatch方法
store.dispatch = function dispatchAndLog(action) {
  //前后添加日志
  console.log('dispatching', action);
  next(action);//执行dispatch
  console.log('next state', store.getState());
}
```


####中间件的使用

以redux-thunk中间件为例，redux-thunk是一个处理异步任务（耗时操作）的中间件，常用于store.dispatch一些耗时操作或网络请求的场景。Action Creator默认是返回对象的，redux-thunk返回的函数。

在构造store时添加中间件支持
多个中间件可能和顺序有关


```
import { createStore, applyMiddleware } from 'redux';
import thunk from 'redux-thunk';
import counter from './index.redux';
 
const store = createStore(
  counter,
  applyMiddleware(thunk)
);

```
模拟耗时操作，发送异步Action


```
setTimeout(() => {
  this.props.dispatch({ type: 'WAIT_5_SECOND' })
}, 5000)
```


实际应用场景


```
function getUserDetail (userId) {
  return (dispatch, getState) => {
    if (getState().user.id === userId) 
    {
      // store中的user和目标user一致，无需重复获取
      return;
    }
    
    //开始请求
    dispatch({type: 'USER_DETAIL_REQUEST', payload: userId});
    fetch(`${API_ROOT}/user/${userId}`)
      .then(res => res.json())
      .then(res => {
        // 请求成功，发送成功Action
        dispatch({type: 'USER_DETAIL_REQUEST_SUCCESS', payload: res});
      })
      //请求失败，发送失败Action
      .catch(err => dispatch({type: 'USER_DETAIL_REQUST_FAILURE', payload: err})
  };
}
// 获取用户详情
store.dispatch(getUserDetail(100));
```



####常用的Redux中间件
redux-logger

redux-promise （Action Creator可以promise的异步处理中间件）

redux-saga（异步处理）
