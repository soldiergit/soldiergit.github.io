---
title: 面试题—Vue框架面试总结
tags: [面试题, Java面试题, Vue， 框架]
index_img: /resource/img/Vue.png
date: 2020-11-25 15:49:57
---

## vuex的作用？
vuex 是一个专门为 vue.js 应用程序开发的状态管理模式，可以帮助我们管理共享状态，也就是`管理全局变量`。

vuex 使用一个 store 对象管理应用的状态，一个 store包括：state、getter、mutation、action、modules 五个属性
 - **state**：意为‘状态’，是 vuex 状态管理的数据源；
 - **getter**：作用与 filters 有一些相似，可以将 state 进行过滤后输出；
 - **mutation**：是 vuex 中改变state 的唯一途径，并且只能同步操作；
 - **action**：一些对 state 的异步操作可以放在 action 中，并通过在 action 中提交 mutation 变更状态；
 - **modules**：模块化vuex，可以让每一个模块拥有自己的 state、mutation、action、getters，使得结构非常清晰，方便管理。

我们可以在组件中触发 action，action 则会提交 mutation，mutation 会对 state 进行修改，组件再根据 state、getter 渲染页面。

## vue 中路由拦截器的作用？
作用是：路由拦截、权限设置。

例如：当用户没有登录或后台 token 失效时就会跳转到登录页面，用到的字段`requireAuth:true`。
```vue
router.beforeEach((to,from,next)=>{
    if(to.path=='/login' || localStorage.getItem('token')){
        next();
    }else{
        alert('请重新登录');
        next('/login');
    }
})
```

## axios 的作用？
`axios`：是由 promise 封装的一个 http 的库，相当于 vue 中的 ajax，用于向后台发送请求。特点是：
 - 从浏览器中创建 XMLHttpRequests；
 - 从 node.js 中创建 http 请求；
 - 支持 Promise API；
 - 拦截请求和响应；
 - 转换请求数据和响应数据；
 - 取消请求；
 - 自动转换 json 数据；
 - 客户端支持防御 XSRF 攻击。

`Promise`： 是异步编程的一种解决方案，比传统的解决方案（回调函数和事件）更合理和更强大。它由社区最早提出和实现，ES6将其写进了语言标准，统一了语法，原生提供了 Promise。
Promise 的本质是分离异步数据获取和业务。

我们可以使用 Promise 配合 axios 实现拦截器：
1. 请求拦截器
```vuejs
axios.interceptors.request.use(function(config){
    return config;
},function(error){
    return Promise.reject(error);
});
```
2. 响应拦截器
```vuejs
axios.interceptors.response.use(function(response){
    return response;
},function(error){
    return Promise.reject(error);
});
```

## 列举 vue 的常见指令
1. `{{}}`：文本插值
```vue
<div id="app">
    {{message}}
</div>
```
2. `v-bind`：DOM 属性绑定
```vue
<div id="app">
    <span v-bind:title='message'>
        鼠标悬停几秒钟查看此处动态绑定的提示信息
    </span>
</div>
```
3. `v-on`：绑定一个事件监听器
```vue
<div id="app">
    <button v-on:click='deleteHandler'>删除</button>
</div>
```
4. `v-model`：实现表单输入和应用状态的双向数据绑定
```vue
<div id="app">
    <p>{{message}}</p>
    <input v-model='message'>
</div>
```
5.` v-if 和 v-else`：控制元素的显示，动态的向DOM树内添加或者删除DOM元素（条件不成立时不会出现代码）
```vue
<div id="app">
    <p v-if='isStudent'>我是学生</p>
</div>
```
6. `v-show`：控制元素的显示，本质就是通过设置 css 中的 display 设置为 none，控制隐藏（条件不成立时会出现代码）
```vue
<div id="app">
    <p v-show='isStudent'>我是学生</p>
</div>
```
7. `v-for`：列表渲染
```vue
<div id="app">
    <template v-for="stu in studentList">
        <span v-text="stu.name"></span>
    </template>
</div>
```

## 对于 MVVM 的理解
Vue 是一个基于 MVVM（Model-View-ViewModel）模式实现的轻量级的渐进式前端框架，它提供了一种帮助开发者快速构建并开发前端项目的新的思维模式。
 - **Model**：代表数据模型，也可以在 Model 中定于数据修改和操作的业务逻辑；
 - **View**：代表 UI 组件，它负责将数据模型转化为 UI 展示出来；
 - **ViewModel**：监听模型数据的改变和控制视图行为、处理用户交互，简单理解就是一个同步 View 和 Model 的对象，连接 Model 和 View。

ViewModel 和 Model 实现双向数据绑定。

## Vue的生命周期
1. 什么是 Vue 的生命周期？
`答：`Vue 实例从创建到销毁的过程，就是生命周期。从开始创建、编译模板、挂载DOM &rarr; 渲染、更新 &rarr; 渲染、销毁等一系列过程，称之为 Vue 的生命周期。
2. Vue 生命周期的作用是什么？
`答：`它的生命周期中有多个事件钩子，让我们在控制整个 Vue 实例的过程时更容易形成好的逻辑。
3. Vue 生命周期总共有几个阶段？
`答：`它可以总共分为8个阶段：创建前、后 &rarr; 转入前、后 &rarr; 更新前、后 &rarr; 销毁前、后。
4. 第一次页面加载会触发哪几个钩子？
`答：`会触发beforeCreate、created、beforeMount、mounted。
5. DOM 渲染在哪个周期中就已经完成？
`答：`DOM 渲染在 mounted 中就已经完成了。

## Vue 实现数据双向绑定的原理是什么？
`答：`Object.defineProperty（）。采用数据劫持结合发布者-订阅者模式的方式，通过Object.defineProperty（）来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应监听回调。

## Vue 组件间的参数传递
1. 父组件与子组件传值
    1.1 父组件传给子组件：子组件通过 `props 方法`接受数据;
    1.2 子组件传给父组件：`$emit 方法`传递参数
2. 非父子组件间的数据传递，兄弟组件传值
eventBus，就是创建一个事件中心，相当于中转站，可以用它来传递事件和接收事件。项目比较小时，用这个比较合适。（虽然也有不少人推荐直接用VUEX，具体来说看需求咯。技术只是手段，目的达到才是王道。）

## 怎么定义 vue-router 的动态路由? 怎么获取传过来的值
在 router 目录下的 index.js 文件中，对 path 属性加上 /:id，使用 router 对象的 params.id 获取。

## vue常用的修饰符？
 - .prevent: 提交事件不再重载页面；
 - .stop: 阻止单击事件冒泡；
 - .self: 当事件发生在该元素本身而不是子元素的时候会触发；
 - .capture: 事件侦听，事件发生的时候会调用

## vue.js的两个核心是什么？
数据驱动、组件系统
