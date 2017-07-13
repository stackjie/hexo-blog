---
title: vue-router最佳实践
date: 2017-07-13 23:48:19
tags:
---
vue-router已经是开发vue项目中不可缺少的核心插件，本文是自己在项目经验中的一些vue-router的最佳实践和网上的一些博文结合的总结。

## keep-alive & transition
`keep-alive`和`transition`做为vue中比较重要也比较常用的api可以和vue-router提供的`router-view`组件配合使用，当路由切换时`keep-alive`可以缓存`router-view`中生成的vue组件避免重复渲染，`transition`提供路由改变时动画过渡的钩子。

### keep-alive
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

#### 最佳实践

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
