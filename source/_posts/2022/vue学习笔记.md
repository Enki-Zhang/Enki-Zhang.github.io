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

## vscode启动脚手架

cd 到项目想要创建的文件夹下面 `vue create 文件名` 启动脚手架

![asset_img](vue学习笔记/2022-06-20-16-09-48.png)

`npm run serve` 启动vue 

![asset_img](vue学习笔记/2022-06-20-16-11-24.png)

效果

![asset_img](vue学习笔记/2022-06-20-16-11-42.png)

## 关于SPA(single-page application)

简单了解下SPA: **单页应用**（英语：single-page application，缩写SPA）是一种网络应用程序或网站的模型，它通过动态重写当前页面来与用户交互，而非传统的从服务器重新加载整个新页面。这种方法避免了页面之间切换打断用户体验，使应用程序更像一个桌面应用程序 -------维基百科

## vue路由相关

## 添加欢迎页面、登录页面

参考链接
https://segmentfault.com/q/1010000007897556

## hash 和 history model模式  


## 
 项目中已经删除组件newIndex.vue
 ![asset_img](vue学习笔记/2022-06-22-14-37-31.png)
 报错如下
 ![asset_img](vue学习笔记/2022-06-22-14-38-52.png)


v-for中的key 作用？


```vue
<template>
<!--这里写 html -->
<template/>
<script>
 export default {};
 // 这里写js
</script>
<style lang = "less" scoped>
 <!--这里写css-->
</style>
```
一个.vue 文件就等于单独组件。因为.vue文件是自定义的，浏览器不识别，所以要对该文件进行解析,在webpack构建中，需要安装vue-loader 对.vue文件进行解析。

template里面最外层必须是只有一个容器
script 中的 export default {} 即导出这个组件，外部可以引用。
style 中的 lang 指额外表示支持的语言可以让编辑器识别,scoped 指这里写的css只适用于该组件

导航是路由的切换
router.beforeEach(async (to, from, next)
next判断能否进入目标路由


push 和 apply 的区别

router.push 重定向报错问题
![asset_img](vue学习笔记/2022-08-24-16-10-45.png)

axios 二次封装的原因

将公共组件隐藏和显示

p3没有做axios的封装

跨域 协议域名端口号不同请求 称为跨域
webpack.config.js文件相当于vue.config.js文件、

没有noprogress进度条

vuex 当项目比较大，组件通信数据比较复杂，这种情况在使用vuex

Vuex是插件，通过vuex仓库进行存储项目的数据 将大仓库转为小仓库按照模块化进行处理

state{} 仓库存储数据的地方
mutation{}  修改state的唯一手段
action{} 处理action书写 自己的业务逻辑 可以处理异步
getters{}  计算属性 简化仓库数据让组件获取仓库的数据更加方便

函数的防抖和节流
节流 在规定的间隔时间范围内不会重复触发回调 只有大于这个时间间隔才会触发回调 把频繁的触发变为少量的触发 _.throttle()
防抖  前面的所有触发都被取消 最后一次执行在规定的时间之后才会触发 连续快速触发 只会执行一次
 
利用lodash 插件 包含防抖和节流业务
对外暴露的函数为_function 不用箭头函数
按需引入 优化项目 

声明式导航 出现卡顿  会一直将router-link组件不断创建实例 耗费内存出现卡顿
编程式导航+事件委派 事件委派 全部子节点的事件委派到父节点 点击a标签时进行路由跳转 如何确定点击的是a标签？ 在子标签中加上标示 自定义属性 可以获取当前节点 利用节点的dataset获取标示
如何确定是几级标签

编程式导航是利用组件的$router.push|place方法 跳转

2)组件name属性的作用?
2.1开发者工具中可以看见组件的名字
2.2注册全局组件的时候，可以通过组件实例获取相应组件的名字


##  路由参数
~~~js
  methods: {
    goSearch() {
      //路由参数传递
      // 字符串
      // this.$router.push(
      //   "/search/" + this.keyword + "?k=" + this.keyword.toUpperCase()
      // );
      // 模板字符串
      // this.$router.push(
      //   `/search/${this.keyword}?k=${this.keyword.toUpperCase()}`
      // );
      // 对象 给路由起个名字name
      this.$router.push({
        name: "search",
        params: {
          keyword: this.keyword,
        },
        query: { k: this.keyword.toUpperCase() },
      });
    },
  },
~~~
![asset_img](vue学习笔记/2022-08-26-12-52-20.png)
![asset_img](vue学习笔记/2022-08-26-12-52-44.png)
## 编程式导航路由跳转
I:编程式路由跳转到当前路由（参数不变），**多次执行**会抛出NavigationDuplicated的警告错误?
--路由跳转有两种形式：声明式导航、编程式导航
--声明式导航没有这类问题的，因为vue-router底层已经处理好了
1.1为什么编程式导航进行路由跳转的时候，就有这种警告错误那？
"vue-router":"@3.5.3":最新的vue-router引入promise 需要传递resolve和reject参数

可以传递reject参数捕获err 
![asset_img](vue学习笔记/2022-08-26-13-03-24.png)
![asset_img](vue学习笔记/2022-08-26-13-03-11.png)
这种方法每次都要手动传递参数 

三级联动 
注册全局组件 注册一次全局使用 home search detail

## axios封装
原因：不过随着项目规模增大，如果每发起一次HTTP请求，就要把这些比如设置超时时间、设置请求头、根据项目环境判断使用哪个请求地址、错误处理等等操作，都需要写一遍，这种重复劳动不仅浪费时间，而且让代码变得冗余增加，难以维护。为了提高代码质量，所以应该在项目中二次封装 axios 再使用。

7.1跨域问题
什么是跨域：协议、域名、端口号不同请求，称之为跨域


## vuex 
Vuex是官方提供一个插件，状态管理库，集中式管理项目中组件共用的数据
![asset_img](vue学习笔记/2022-08-26-21-13-19.png)

数据绑定


v-model
>语法糖：指计算机语言中添加的某种语法，这种语法对语言的功能并没有影响，但是更方便程序员使用。通常来说使用语法糖能够增加程序的可读性，从而减少程序代码出错的机会。
v-model 适用于表单元素 有value元素
1.双向绑定一般都应用在表单类元素上（如：input、se1ect等）
2.v-model:value可以简写为v-model,因为v-model默认收集的就是value值。


计算属性 将属性进行加工称为新的属性

watch 类似计算属性 $watch
![asset_img](vue学习笔记/2022-08-29-15-12-48.png)


或者用vm.$watch('监视内容',function(){})function类似handler


vue的v-for的key

key没有指定或者指定为index（diff算法）
 ![asset_img](vue学习笔记/2022-08-29-19-50-55.png)
 2,对比规则：
(1).旧虚拟DoM中找到了与新虚拟DoM相同的key:
1.若虚拟D0M中内容没变，直接使用之前的真实D0M!
2，若虚拟D0M中内容变了，则生成新的真实D0M,随后替换掉页面中之前的真实D0M。
(2).旧虚拟DoM中未找到与新虚拟DOM相同的key
创建新的真实D0州，随后渲染到到页面。
 用index作为key可能会引发的问题：
1.若对数据进行：逆序添加、逆序删除等破坏顺序操作：
会产生没有必要的真实D0州更新==>界面效果没问题，但效率低。
2.如果结构中还包含输入类的D0M:
会产生错误D0M更新==>界面有问题。
开发中如何选择key?:
1.最好使用每条数据的唯一标识作为key,比如id、手机号、身份证号、学号等唯一值。
2.如果不存在对数据的逆序添加、逆序删除等破坏顺序操作，仅用于渲染列表用于展示，
使用index作为key是没有问题的。

this.$set()不能在vm的实例添加数据不能直接在data中添加


## 生命周期
![asset_img](vue学习笔记/2022-08-30-10-56-09.png)
生命周期：
1.又名：生命周期回调函数、生命周期函数、生命周期钩子。
2.是什么：Vue在关键时刻帮我们调用的一些特殊名称的函数。
3.生命周期函数的名字不可更改，但函数的具体内容是程序员根据需求编写的。
4.生命周期函数中的this指向是vm或组件实例对象。
vm重要的钩子
(重要)挂载完毕==>调用mounted函数。=>【重要的钩子】
(重要)
将要销毁==>调用beforeDestroy函数。======>【重要的构子】

常用的生命周期钩子：
1.mounted:发送ajax请求、启动定时器、绑定自定义事件、订阅消息等【初始化操作】。
2.beforeDestroy:清除定时器、解绑自定义事件、取消订阅消息等【收尾工作】。
关于销毁Vue实例
1,销毁后借ue开发者工具看不到任何信息。
2.销毁后自定义事件会失效，但原生D0州事件依然有效。
3.一般不会再beforeDestroy操作数据，因为即便操作数据，也不会再触发更新流程了。

组件
1.关于组件名：
一个单词组成：
第一种写法（首字母小写）：schoo1
第二种写法（首字母大写）：Schoo1
多个单词组成：
第一种写法(kebab-case命名)：my-school
第二种写法(CamelCase命名)：MySchoo1(需要Vue脚手架支持)
备注：
(1).组件名尽可能回避HTML中已有的元素名称，例如：2、H2都不行。
(2).可以使用name配置项指定组件在开发者工具中呈现的名字。(和注册的名称无关)
2.关于组件标签：
第一种写法：`<schoo1></schoo1>`
第二种写法：`<schoo1/>`
备注：不用使用脚手架时，<scoo1/>会导致后续组件不能渲染。

4.关于this指向：
(1).组件配置中：
data函数、methods中的函数、watch中的函数、computed中的函数它们的this均是【VueComponent实例对象】
(2).new Vue(options)配置中：
data函数、methods中的函数、watch中的函数、computed中的函数它们的this均是【Vue实例对象】。

![asset_img](vue学习笔记/2022-08-30-20-10-17.png)
表示动态绑定中的""中时js表达式

props,是只读的，Vue底层会监测你对props的修改，如果进行了修改，就会发出警告，若业务需求确实需要修改，那么请复制props的内容到data中一份，然后去修改data中的数据。

mixin 混入
插件


scoped样式
作用：让样式在局部生效，防止冲突。
写法：`<style scoped>`


浏览器本地存储 localStorage存储在本地 浏览器关闭不会清空
sessionStorage关闭浏览器 清空

组件中区分子父组件

import组件的组件为父组件 被import的为子组件

默认插槽`<slot>`