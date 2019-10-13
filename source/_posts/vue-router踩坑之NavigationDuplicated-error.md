---
title: vue-router踩坑之NavigationDuplicated error
date: 2019-08-21 18:31:12
categories: 
- vue-touter
- 开发遇到的问题
tags:
- vue-router
- vue
---
### 前言

&emsp;&emsp;因业务需求重新开发了一个项目，但是在使用`vue-router`，偶然发现`vue-router`会报下面这个错误。

![\[外链图片转存失败(img-MvBC79rI-1566403322600)(evernotecid://B04430E3-2296-4CE6-91FA-09EA592E652D/appyinxiangcom/12982926/ENResource/p2)\]](https://user-gold-cdn.xitu.io/2019/8/22/16cb4f296c3e2afb?w=836&h=85&f=png&s=27772)

后来网上查询，给出了很多解决方案，很多都说直接删除node_modules，重新安装依赖就好。同时也有很多网友说这个方案无效。其实会出现这种错误的情况，是因为
```
[Bug Report] 升级vue-router至3.1以后版本，导航组件重复点击报错 NavigationDuplicated
```
也就是说这是`vue-router`3.1版本之后，对于航组件重复点击的一个错误处理方式。

##### 因此，要解决这个报错有两种方案：
1. 将`vue-router`版本锁在3.1版本以下

```
// 卸载原来的vue-router
npm un vue-router
// 安装指定版本(3.1以下)的vue-router，我选择装3.0.6
npm i vue-router@3.0.6
```

2. 使用router.push时，请进行`catch`处理，将这个错误捕获。


#### 原讨论贴
地址：[No stacktrace on NavigationDuplicated error #2881
](https://github.com/vuejs/vue-router/issues/2881)

![\[外链图片转存失败(img-1bysQ27x-1566403322602)(evernotecid://B04430E3-2296-4CE6-91FA-09EA592E652D/appyinxiangcom/12982926/ENResource/p3)\]](https://user-gold-cdn.xitu.io/2019/8/22/16cb4f296c1f8f7a?w=765&h=890&f=png&s=294987)
