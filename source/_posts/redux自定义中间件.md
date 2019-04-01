---
layout: Redux
title: Redux自定义中间件
date: 2018-06-13 11:19:47
categories: 
- React
tags: React-Redux
---

  由于业务逻辑的多样性，可能开源的一些第三方的中间件无法满足我们的需求，我就需要根据自己的业务逻辑，去自定义适合我们业务处理的中间件。<!--more-->

###定义中间件


```
let MyMiddleware = store => next => action =>{
    console.log('执行到我的中间件');
    if (action.type === 'my')
    {
        return next(action);
    }
};
export default MyMiddleware;
```


store =>next => action => 其实就是一层一层的向外返回action;


###添加到store中


```
import {createStore , applyMiddleware} from 'redux';
import MyMiddleware from './MyMiddleware';
let store = createStore(counter , applyMiddleware(MyMiddleware));
```