---
title: vue学习笔记
top: false
cover: false
toc: true
mathjax: true
date: 2022-06-19 20:23:35
password:
summary:
tags: vue
categories: 前端
---

<!-- # vue 学习记录 -->

## vscode 启动脚手架

cd 到项目想要创建的文件夹下面 `vue create 文件名` 启动脚手架

![asset_img](vue学习笔记/2022-06-20-16-09-48.png)

`npm run serve` 启动 vue

<!-- more -->

效果

## vue 目录结构

![asset_img](vue学习笔记/2022-09-06-13-33-00.png)

## vue 路由相关

this.\$route 和 this.$router 的区别

### 路由参数

params 参数：属于路径当中的一部分，需要注意，在配置路由的时候，需要占位
query 参数：不属于路径当中的一部分，类似于 ajax 中的 querystring /home?k=v&kv=, 不需要占位

```js
this.$router.push(
  {
    name: "search",
    params: {
      // 替换掉路径的keyword path: "/search/:keyword",
      keyword: this.keyword,
    },
    // 路径中添加？AS=keyword  path: /search/:keyword?(AS)...,
    query: {
      AS: this.keyword.toUpperCase(),
    },
  },
  (err) => console.log(err)
);
```

![asset_img](vue学习笔记/2022-08-26-12-52-20.png)

![asset_img](vue学习笔记/2022-08-26-12-52-44.png)

> 需要注意使用 params 时候 path 必须使用 name 指定

## 编程式导航多次跳转时提示错误

I: 编程式路由跳转到当前路由（参数不变），**多次执行**会抛出 NavigationDuplicated 的警告错误?
--路由跳转有两种形式：声明式导航、编程式导航
--声明式导航没有这类问题的，因为 vue-router 底层已经处理好了
1.1 为什么编程式导航进行路由跳转的时候，就有这种警告错误那？
"vue-router":"@3.5.3": 最新的 vue-router 引入 promise 需要传递 resolve 和 reject 参数

可以传递 reject 参数捕获 err

![asset_img](vue学习笔记/2022-08-26-13-03-24.png)

![asset_img](vue学习笔记/2022-08-26-13-03-11.png)

这种方法每次都要手动传递参数

将 push 重写

```js
const originalPush = VueRouter.prototype.push;
VueRouter.prototype.push = function push(location, onResolve, onReject) {
  // console.log(`push被调用`);
  if (onResolve || onReject)
    return originalPush.call(this, location, onResolve, onReject);
  return originalPush.call(this, location).catch((err) => err);
};
```

## 添加欢迎页面、登录页面

参考链接
<https://segmentfault.com/q/1010000007897556>

## axios 二次封装的原因

原因：不过随着项目规模增大，如果每发起一次 HTTP 请求，就要把这些比如设置超时时间、设置请求头、根据项目环境判断使用哪个请求地址、错误处理等等操作，都需要写一遍，这种重复劳动不仅浪费时间，而且让代码变得冗余增加，难以维护。为了提高代码质量，所以应该在项目中二次封装  axios  再使用。

使用请求和响应拦截器：请求拦截器在请求创建之前进行处理，响应拦截器可以在数据返回时进行处理

## 跨域问题

协议、域名、端口号不同请求，称之为跨域
使用 webpack 的配置
跨域 协议域名端口号不同请求 称为跨域
webpack.config.js 文件相当于 vue.config.js 文件、

```js
module.exports = {
  productionSourceMap: false,
  // 关闭ESLINT校验工具
  lintOnSave: false,
  // 配置跨域问题
  devServer: {
    proxy: {
      "/api": {
        target: "http://gmall-h5-api.atguigu.cn",
        // pathRewrite: { "^/api": "" },
      },
    },
  },
};
```

## 事件委派

声明式导航 出现卡顿 会一直将 router-link 组件不断创建实例 耗费内存出现卡顿
编程式导航+事件委派 事件委派 全部子节点的事件委派到父节点
通过自定义属性标记各级子节点，为以后路由跳转传递参数

> ```<a :data-categoryName="c1.categoryName" :data-category1Id="c1.categoryId">{{c1.categoryName}}</a>
>
> ```

````
>```:data-categoryName```即为自定义绑定属性

>2)组件name属性的作用?
2.1开发者工具中可以看见组件的名字
2.2注册全局组件的时候，可以通过组件实例获取相应组件的名字

## vuex

Vuex是官方提供一个插件，状态管理库，集中式管理项目中组件共用的数据
Vuex是插件，通过vuex仓库进行存储项目的数据 将大仓库转为小仓库按照模块化进行处理

state{} 仓库存储数据的地方
mutation{}  修改state的唯一手段
action{} 处理action书写 自己的业务逻辑 可以处理异步
getters{}  计算属性 简化仓库数据让组件获取仓库的数据更加方便

![asset_img](vue学习笔记/2022-08-26-21-13-19.png)

> 流程

![asset_img](vue学习笔记/2022-09-01-14-09-58.png)

vue Components分发请求

```js
<
script >
    export default {
        name: " ",
        data() {
            return {
                n: 1,
                //   sum: 0,
            };
        },
        methods: {
            plus() {
                //   this.sum += this.n;
                this.$store.dispatch(`plus`, this.n);
            },
            reduction() {
                //   this.sum -= this.n;
            },
            isOdd() {
                /* if (this.sum % 2 != 0) {
                  this.sum++;
                } */
            },
            waiting() {
                setTimeout(() => {
                    // this.sum++;
                }, 3000);
            },
        },
    }; <
/script>
````

states 中响应数据

```js
// 存储数据
const state = { sum: 0 };
```

到 action 中选择方法计算

```js
//响应组件的动作
const actions = {
  plus(context, value) {
    console.log(`actions参数显示`, context, value);
    // 将plus大写提示在mutation中的函数
    context.commit(`PLUS`, value);
  },
};
```

![asset_img](vue学习笔记/2022-09-01-14-16-38.png)

到 mutations 中操作数据

```js
// 操作数据
const mutations = {
  PLUS(state, value) {
    console.log(`mutations中的plus的参数显示`, state, value);
    // 操作state
    state.sum += value;
  },
};
```

![asset_img](vue学习笔记/2022-09-01-14-19-03.png)

render 到模板中

![asset_img](vue学习笔记/2022-09-01-14-21-55.png)

> > actions 中的函数没有意义可以直接在组件中 commit

![asset_img](vue学习笔记/2022-09-01-14-43-59.png)

getters 使用
当 state 中的数据需要经过加工后再使用时，可以使用 getters 加工。（类似于 computed）

```js
//将state数据进行加工
const getters = {
  bigSum(state) {
    return state.sum * 10;
  },
};
```

组件中读取数据：```$store.getters.bigSum

````

mapState使用 ```computed: { ...mapState([`sum`, `schoolName`]) },``` 并使用ES6简写,表示```sum(){return this.$state.sum}``` ```schoolName(){return this.$state.schoolName}```vue将重复的部分省去使得开发者只需要写函数名和重要的函数体部分

>...mapState({sum,schoolName}) 等同于 ...mapState({sum:sum,schoolName:sum})sum取出来的是变量sum,而不是 sum:`sum` 用在computed中其他的map用在methods中
不需要经过action直接commit的情况

mapMutations ```...mapMutations({ plus: `PLUS` }),```

![asset_img](vue学习笔记/2022-09-01-20-23-31.png)

> this.value值需要在模板中传```<button @click="plus(n)">+</button>

````

平时如果不写 plus()写成 plus 表示 plus(event)默认传入的 event

简写形式`` ...mapMutations([`PLUS`]), `` 修改模板中的方法

![asset_img](vue学习笔记/2022-09-01-21-21-09.png)

mapActions`` ...mapActions({ isOdd: `isOdd` }), ``相当于`` this.$store.dispatch(`isOdd`, this.$store.state.sum); ``

简写形式 ` ` `...mapActions([` isOdd` ]),

![asset_img](vue学习笔记/2022-09-01-21-33-37.png)

modules 管理：
将 store 进行模块化管理

![asset_img](vue学习笔记/2022-09-02-14-40-56.png)

```js
const childQ = {
  namespaced: true,
  state: {
    sum: 1,
    schoolName: `上学`,
  },
  actions: {
    isOdd(context, value) {
      if (context.state.sum % 2) {
        context.commit(`ISODD`, value);
      }
    },
    waiting(context, value) {
      setTimeout(() => {
        context.commit(`WAITING`);
      }, 3000);
    },
  },
  mutations: {
    PLUS(state, value) {
      console.log(`mutations中的plus的参数显示`, state, value);
      state.sum += value;
    },
    ISODD(state, value) {
      state.sum++;
    },
    REDUCTION(state, value) {
      state.sum--;
    },
    WAITING(state, value) {
      state.sum++;
    },
  },
};

export default childQ;
```

在 index.js 中使用

```js
export default new vuex.Store({
  modules: {
    childQ,
    person,
  },
});
```

取出单个 module 中的数据 需要 namespaced:true

![asset_img](vue学习笔记/2022-09-02-14-43-46.png)

vue-router 和 vue 的版本需要匹配否则报错

![asset_img](vue学习笔记/2022-09-02-15-41-56.png)

> 组件命名用大驼峰

## 函数的防抖和节流

节流 在规定的间隔时间范围内不会重复触发回调 只有大于这个时间间隔才会触发回调 把频繁的触发变为少量的触发 \_.throttle()

```js
 onSearch: _.debounce(function() {
     console.log(`发送请求`);
 }, 1000),
```

防抖 前面的所有触发都被取消 最后一次执行在规定的时间之后才会触发 **_连续快速触发 只会执行一次_**

```js
    add: _.throttle(function() {
        this.count++;
        console.log(`节流执行`);
    }, 10000),
```

> 利用 lodash 插件 包含防抖和节流业务

对外暴露的函数为\_function 不用箭头函数
按需引入 优化项目

## 自定义指令

对象式

![asset_img](vue学习笔记/2022-09-09-10-43-42.png)

函数式

![asset_img](vue学习笔记/2022-09-09-10-44-29.png)

```js
 directives: {
     focus: function(el, value) {},
 },
```

## 事件委派

当事件的循环次数很大，并且每次循环的事件都有绑定事件时，会非常消耗内存。这时候可以利用冒泡的机制和事件流的特性将事件绑定在父节点上
数据绑定

## mock 数据

拦截 ajax 请求
新建 mock 文件夹 创建 json 数据

![asset_img](vue学习笔记/2022-09-09-16-42-33.png)

创建 api 文件

![asset_img](vue学习笔记/2022-09-09-16-43-04.png)

ajax 请求

```js
export const reqBannerList = () => {
  return mockRequest.get("/banner");
};
```

> 注意![asset_img](vue学习笔记/2022-09-15-16-36-44.png)
> 不能写成 this.state

在组件中向 vuex 请求数据
v-model

> 语法糖：指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。

v-model 适用于表单元素 有 value 元素

1. 双向绑定一般都应用在表单类元素上（如：input、se1ect 等）
   2.v-model:value 可以简写为 v-model, 因为 v-model 默认收集的就是 value 值。

计算属性 将属性进行加工称为新的属性

watch 类似计算属性 $watch

![asset_img](vue学习笔记/2022-08-29-15-12-48.png)

或者用 vm.$watch('监视内容', function(){})function 类似 handler

vue 的 v-for 的 key

key 没有指定或者指定为 index（diff 算法）

![asset_img](vue学习笔记/2022-08-29-19-50-55.png)

2, 对比规则：
(1). 旧虚拟 DoM 中找到了与新虚拟 DoM 相同的 key:

1. 若虚拟 D0M 中内容没变，直接使用之前的真实 D0M!
   2，若虚拟 D0M 中内容变了，则生成新的真实 D0M, 随后替换掉页面中之前的真实 D0M。
   (2). 旧虚拟 DoM 中未找到与新虚拟 DOM 相同的 key
   创建新的真实 D0 州，随后渲染到到页面。
   用 index 作为 key 可能会引发的问题：
1. 若对数据进行：逆序添加、逆序删除等破坏顺序操作：
   会产生没有必要的真实 D0 州更新==>界面效果没问题，但效率低。
1. 如果结构中还包含输入类的 D0M:
   会产生错误 D0M 更新==>界面有问题。
   开发中如何选择 key?:
1. 最好使用每条数据的唯一标识作为 key, 比如 id、手机号、身份证号、学号等唯一值。
1. 如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，
   使用 index 作为 key 是没有问题的。

this.$set()不能在 vm 的实例添加数据不能直接在 data 中添加

## 生命周期

![asset_img](vue学习笔记/2022-08-30-10-56-09.png)

生命周期：

1. 又名：生命周期回调函数、生命周期函数、生命周期钩子。
2. 是什么：Vue 在关键时刻帮我们调用的一些特殊名称的函数。
3. 生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的。
4. 生命周期函数中的 this 指向是 vm 或组件实例对象。
   vm 重要的钩子
   (重要)挂载完毕==>调用 mounted 函数。=>【重要的钩子】
   (重要)
   将要销毁==>调用 beforeDestroy 函数。======>【重要的构子】

常用的生命周期钩子：
1.mounted: 发送 ajax 请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】。
2.beforeDestroy: 清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】。
关于销毁 Vue 实例
1, 销毁后借 ue 开发者工具看不到任何信息。 2. 销毁后自定义事件会失效，但原生 dom 事件依然有效。 3. 一般不会再 beforeDestroy 操作数据，因为即便操作数据，也不会再触发更新流程了。

组件

1. 关于组件名：
   一个单词组成：
   第一种写法（首字母小写）：schoo1
   第二种写法（首字母大写）：Schoo1
   多个单词组成：
   第一种写法(kebab-case 命名)：my-school
   第二种写法(CamelCase 命名)：MySchoo1(需要 Vue 脚手架支持)
   备注：
   (1). 组件名尽可能回避 HTML 中已有的元素名称，例如：2、H2 都不行。
   (2). 可以使用 name 配置项指定组件在开发者工具中呈现的名字。(和注册的名称无关)
2. 关于组件标签：
   第一种写法： `<schoo1></schoo1>`

第二种写法： `<schoo1/>`

备注：不用使用脚手架时，<scoo1/>会导致后续组件不能渲染。

4. 关于 this 指向：
   (1). 组件配置中：
   data 函数、methods 中的函数、watch 中的函数、computed 中的函数它们的 this 均是【VueComponent 实例对象】
   (2).new Vue(options)配置中：
   data 函数、methods 中的函数、watch 中的函数、computed 中的函数它们的 this 均是【Vue 实例对象】。

![asset_img](vue学习笔记/2022-08-30-20-10-17.png)

表示动态绑定中的""中时 js 表达式

## 组件传递信息

![asset_img](vue学习笔记/2022-09-15-16-16-18.png)
![asset_img](vue学习笔记/2022-09-15-16-17-56.png)
props, 是只读的，Vue 底层会监测你对 props 的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制 props 的内容到 data 中一份，然后去修改 data 中的数据。

mixin 混入
插件

scoped 样式
作用：让样式在局部生效，防止冲突。
写法： `<style scoped>`

浏览器本地存储 localStorage 存储在本地 浏览器关闭不会清空
sessionStorage 关闭浏览器 清空

组件中区分子父组件

import 组件的组件为父组件 被 import 的为子组件

默认插槽 `<slot>`

vue3.0 对应的是 vuex 的 4 版本 vue2.0 对应 vuex 的 3 版本

route

    1. 路由组件通常存放在pages文件夹，一般组件通常存放在components文件夹。
    2. 通过切换，“隐藏”了的路由组件，默认是被销毁掉的，需要的时候再去挂载。
    3. 每个组件都有自己的$oute属性，里面存储着自己的路由信息。
    4. 整个应用只有一个router, 可以通过组件的$router属性获取到。

children 路由

```js
{
    path: "/login",
    component: Login,
    meta: {
        show: false
    },
    children: [{
        path: "demo03", //不加/
        component: Demo03,
    }, ],
},
```

路由参数

```js

    <!-- 跳转路由 字符串写法 -->
    <router-link
      :to="{
        path: `/home01/message/detail` ,
        query: {
          id: 1,
          title: `zhangsan` ,
        },
      }"
    >
      <button>跳转</button>
    </router-link>

```

命名路由
简化路由路径

![asset_img](vue学习笔记/2022-09-02-21-23-32.png)

![asset_img](vue学习笔记/2022-09-02-21-25-18.png)

param 参数

![asset_img](vue学习笔记/2022-09-02-21-34-14.png)

特别注意：路由携带 params: 参数时，若使用 to 的对象写法，则不能使用 path 配置项，必须使用 name 配置

![asset_img](vue学习笔记/2022-09-02-21-48-14.png)

![asset_img](vue学习笔记/2022-09-02-21-53-11.png)

> params 方式和 query 方式的区别

query 方式生成的 url 为/xx?id=id，params 方式生成的 url 为 xx/id
path 只能使用 query 方式
params 方式需要注意的是需要定义路由信息如：path: '/xx/:id', 这样才能进行携带参数跳转，否则 url 不会进行变化，并且再次刷新页面后参数会读取不到

replace 将当前路径替换掉历史记录 push 将历史记录保存

`<router-link>` 的 replace 属性

1. 作用：控制路由跳转时操作浏览器历史记录的模式
2. 浏览器的历史记录有两种写入方式：分别为 push 和 replace,
   push 是追加历史记录，replace 是替换当前记。路由跳转时候默认为 push
3. 如何开启 replace 模式：
   `<router-link replace .......>` News `</router-link>`

路由缓存防止路由跳转后数据消失 `<keep-alive include="组件名称">` 缓存多个用数组
路由守卫
对路由权限的校验
