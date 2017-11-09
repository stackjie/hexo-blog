---
title: 响应式web开发的一些笔记
date: 2016-9-14 00:27:26
tags:
---
## 响应式网站3大特征
1.  弹性网格布局 flexible grid layout 
2.  弹性图片 flexible image 
3.  媒体查询 media queries

## 媒体查询

### 基本的CSS3媒体查询用法
`@media all and (min-width:800px) and (orientation:landscape){   }`
- 根据表达式的结果是否应用样式
- all为媒体类型如果不写默认为all
- 可用 and,or,not 逻辑操作符

### 为了保证兼容性only最好不要省略
 ` media="only screen and (min-width:401px) and (max-width:600px)"`
旧浏览器解析：` media="only" `

### CSS3媒体属性
- width:视口宽度
- height:视口高度
- device-width:渲染表面的宽度，就是设备屏幕的宽度
- device-height:渲染表面的高度，就是设备屏幕的高度
- orientation:检查设备处于横向还是纵向
- aspect-ratio:基于视口宽度和高度的宽高比 如：16:9 , 4:3
- color:每种颜色的位数bits 如:min-color:16位，8位
- resolution:检测屏幕或打印机的dpi 如:min-resolution:300dpi

**以上属性都可以添加 `min-` 和 `max-`前缀**

## viewport区别
- width:视口宽度
- device-width:设备屏幕的宽度

### 手机上有三个viewport 视口
- 布局视口（layout viewport）
  - 布局视口是手机浏览器提供的一个布局宽高，当加载网页时首先在布局视口里加载，然后在根据手机屏幕分辨率缩放加载到可视视口中
- 可视视口（visual viewport）
  - 可视视口也就是显示的视口，用户可以缩放操作
- 理想视口（ideal viewport）
  - 理想视口就是让布局视口等于可视视口，理想视口为构建手机浏览器优化的页面添加的，不需要默认的布局视口带来的缩放

**使用理想视口**`<meta name="viewport " content="width=device-width" />` 布局视口成为理想视口

**修改理想视口又禁用缩放的写法**
```
<meta name="viewport " content="width=device-width,
minimum-scale=1.0,    //最小的缩放比例
minimum-scale=1.0,    //最大的缩放比例
user-scalable=no "/>    //禁用缩放
```
## 一些初始工作

### meta声明
`<meta http-equiv="X-UA-Compatible" content="IE=edge">`   "ie=edge"表示强制启用ie最新的浏览器模式渲染页面
` <meta name="viewport" content="width=device-width, initial-scale=1">`  同一视口将默认缩放设置为1

### IE表达式的应用
因为响应式开发用到了CSS3的媒体查询以及一些HTML新标签IE8及已下版本不兼容，利用IE表达式告知用户升级浏览器
`<!--[if gt > /lt < /get >= /lte <= IE 8]><![endif]-->` gt/lt/gte/lte的区别
```
<!--[if lte IE 8]>
<p>升级你的浏览器</p>
<![endif]-->
```
## 响应式设计中的字体单位（px、em、rem的区别）
px单位是为了精确还原设计稿中的大小，但响应式设计中考虑更多的灵活性一般使用相对单位em和rem。

### em 相对的长度单位

- em 相对参照物为父元素的font-size
- em 具有继承的特点
- 当没有设置font-size时，浏览器会有一个默认的em设置：1em = 16px
- em的缺点：因为继承的特性容易混乱不好控制

### rem

- rem的相对参照物为根元素html，相对于参照物固定不变所以比较好计算
- 当没有设置font-size时，浏览器会有默认的rem设置：1rem = 16px,这点与em是一致的
- rem存在兼容性问题，ie8及以下浏览器不支持


注意：在媒体查询中使用rem或者em单位是根据浏览器默认值，在chrome中小于12px的font-size都会被定义成12px
  
## 响应式图片
加载与用户设备大小相匹配的图片，既快速又不会影响用户的体验。

### 如何实现响应式图片
- js或服务端
- srcset
- srcset配合sizes
- picture
- svg

### srcset基本用法
```
<img class="image" src="img/480.png"
          srcset="img/480.png 480w, img/800.png 800w, img/1600.png 1600w">
```
给img标签设置srcset属性后，浏览器会自动根据屏幕大小、DPR、网络环境等来选择加载的图片。

### srcset配合sizes
sizes属性是指定srcset参照的大小
```
<img class="image" src="img/480.png"
srcset="img/480.png 480w, img/800.png 800w, img/1600.png 1600w"
sizes="100vw">
```
如果不写sizes属性默认就是参照视口的宽度也就是`sizes=100vw`
sizes也可嵌套媒体查询来使用
`sizes="(min-width:800px) 800px, 100vw)"`
表示当大于或等于800px就以800px作为参照值，小于800px时视口宽度作为参照值

### picture标签
srcset属性都是浏览器自动选择的，picture标签就更为可控一些
```
<picture>
    <source media="(min-width:36em)"
        srcset="img/tiananmen-s.jpg 768w"/>

     <source 
        srcset="img/tiananmen.jpg 1800w"/>
    <img class="image" src="img/tiananmen.jpg">
</picture>
```
浏览器会遍历picture标签里的source选择符合条件的设置

**srcset和picture只支持最新版本的浏览器目前IE的所有版本都不支持，如需要可以通过picturefill.js来解决兼容性问题**
