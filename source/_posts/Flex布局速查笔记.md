---
title: Flex布局速查笔记
date: 2017-07-16 20:26:47
tags:
---
![flexbox图](http://ot6wx4ryg.bkt.clouddn.com/v2-54a0fc96ef4f455aefb8ee4bc133291b_b.png)

本文是自己学习和项目中flex属性的一些速查笔记。

### display: flex | inline-flex;
定义flex容器，行内元素需定义 `inline-flex` 才会生效。

### flex-direction: row | row-reverse | column | column-reverse;
决定主轴的位置及排列方式。
- `row`：行
- `row-reverse`：反转行
- `column`：列
- `column-reverse`：反转列

### flex-wrap: nowrap | wrap | wrap-reverse;
决定flex item换不换行，`nowrap` 为默认值。
- `nowrap`：项目延主轴排列，空间不足时缩放flex item不会换行
- `wrap`: 换行，新行出行当前行的下方
- `wrap-reverse`：换行，新行出行当前行的上方

### flex-flow: {flex-direction} || {flex-wrap};
此属性为 `flex-direction` 和 `flex-wrap` 的简写形式。

### justify-content: flex-start | flex-end | center | space-between | space-around;
定义了flex item在主轴的对齐方式
- `flex-start`：左对齐
- `flex-end`：右对齐
- `center`：居中对齐
- `space-between`：两端对齐
- `space-around`：flex item两侧的间隔相等

### align-items: flex-start | flex-end | center | baseline | stretch;
定义了项目在交叉轴上的对齐方式
