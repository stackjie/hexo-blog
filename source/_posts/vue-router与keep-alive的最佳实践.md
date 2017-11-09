---
title: vue-router与keep-alive的最佳实践
date: 2017-07-13 23:48:19
tags:
---
`keep-alive`做为vue中比较重要也比较常用的api可以和vue-router提供的`router-view`组件配合使用，当路由切换时`keep-alive`可以缓存`router-view`中生成的vue组件避免重复渲染。

## keep-alive
```html
<template>
  <div id="app" @touchmove.prevent>
    <m-header></m-header>
    <tab></tab>
    <keep-alive>
      <router-view></router-view>
    </keep-alive>
    <player></player>
  </div>
</template>
```
以上代码是一个很常见的`keep-alive`使用方式，但在实际项目中并不是所有组件都要缓存，比如一个列表页下的某个详情页，详情页需要从后端拉取数据然后渲染不同的内容，通常我们把和后端交互的ajax代码都放在vue组件实例的`created`生命周期钩子函数中执行，因为使用了`keep-alive`vue组件并未销毁不会重新渲染，自然`created`生命周期的钩子函数也不会在次执行。

### 最佳实践

#### 内嵌 router-view

> <router-view> 组件是一个 functional 组件，渲染路径匹配到的视图组件。<router-view> 渲染的组件还可以内嵌自己的 <router-view>，根据嵌套路径，渲染嵌套组件。

这段文字是vue-router官方文档的一段介绍说明，我们可以通过嵌套`<router-view>`的方式来解决上述问题。

例：
```html
<template>
  <div class="list-page">
    <div class="content"> xxx </div>
    <router-view></router-view>
  </div>
</template>

```

```js
// 路由配置
{
  path: '/recommend',
  component: Recommend,
  children: [
    {
      path: ':id',
      component: Disc
    }
  ]
}
```

根据文档的描述，配置不需要缓存的子页面路由为嵌套路由，然后在父级页面加上`<router-view>`组件，访问子页面路由的时候，就会在父级页面的`<router-view>`中渲染子页面，从而避免了最顶层的`<router-view>`使用`keep-alive`缓存组件。

#### router.meta 属性

```html
<keep-alive>
    <router-view v-if="$route.meta.isKeepAlive"></router-view>
</keep-alive>
<router-view v-if="!$route.meta.isKeepAlive"></router-view>
```

```js
// 路由配置
{
  path: '/recommend',
  component: Recommend,
  meta: {
    isKeepAlive: true
  }
}
```

#### `include` and `exclude`

vue 2.1.0 新增

以下摘至Vue2的官方文档

> include 和 exclude 属性允许组件有条件地缓存。二者都可以用逗号分隔字符串、正则表达式或一个数组来表示，匹配首先检查组件自身的 name 选项，如果 name 选项不可用，则匹配它的局部注册名称（父组件 components 选项的键值）。匿名组件不能被匹配。

例：
```html
<!-- 逗号分隔字符串 -->
<keep-alive include="a,b">
  <component :is="view"></component>
</keep-alive>
<!-- 正则表达式 (使用 v-bind) -->
<keep-alive :include="/a|b/">
  <component :is="view"></component>
</keep-alive>
<!-- Array (use v-bind) -->
<keep-alive :include="['a', 'b']">
  <component :is="view"></component>
</keep-alive>
```