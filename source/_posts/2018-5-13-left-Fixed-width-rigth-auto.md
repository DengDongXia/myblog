---
layout: post
title: 左右布局，左边定宽且右边自适应（项目中常用到）
date: 2018-05-13
tags:
- css
- layout
categories: css
---

## 前提：html结构如下：

```html

<div class="parent">

  <div class="left">

    我是左边

  </div>

  <div class="right">

    我是右边

  </div>

</div>
```

## 方法一：使用float配合overflow:hidden

```css

.left{

  width: 100px;

  float: left;

  background-color: aqua;

}

.right{

  overflow: hidden;

  background-color: brown;

}
```


> 原理：

> 1. 元素如果不设置宽度，则默认继承父元素宽度

> 2. overflow:hidden; 清除左边元素浮动

> 3. 右边元素未设置宽度，则宽度=父元素宽度-左边元素宽度；

> BFC原理：因为BFC内部的元素和外部的元素绝对不会互相影响，因此， 当BFC外部存在浮动时，它不应该影响BFC内部Box的布局，BFC会通过变窄，而不与浮动有重叠。

## 方法二：使用float配合margin-left

```css

.left{

  width: 100px;

  float: left;

  background-color: aqua;

}

.right{

  margin-left: 100px; /*等于左边的宽度*/

  background-color: brown;

}
```

## 方法三：使用float配合width:100%

```css

.left{

  float:left;

  background-color: aqua;

}

.right{

  width:100%;

  background-color: brown;

}
```

## 方法四：float+calc()属性

```css
.left{

  float: left; /*左边左浮动*/

  width: 100px;

  background-color: aqua;

}

.right{

  float: right; /*右边右浮动*/

  width: calc(100% - 100px); /*100%-左边块的宽度*/

  background-color: brown;

}
```

## 方法五：绝对定位

```css
.left{

  position: absolute;

  left: 0;

  width: 100px;

  background-color: aqua;

}

.right{

  position: absolute;

  left: 100px; /*左边的宽度值*/

  width: 100%;

  background-color: brown;

}
```

## 方法六：table布局

```css
.parent{

  display: table;

  width: 100%;

}

.left{

  display: table-cell;

  width: 100px;

  background-color: aqua;

}

.right{

  display: table-cell;

  background-color: brown;

}
```

> 原理：display:table-cell具有以下特性:
> - text-algin,vertical-algin等对齐属性起作用，margin以及宽高百分比值不起作用
会生成虚拟的table,tr包裹住自己，当存在相邻兄弟元素也被设置为table-cell时，则生成虚拟table,tr将其与兄弟元素包裹在一起，并一行等高显示
多个table-cell属性的元素会占忙被设置成display:table的元素的宽度；
> - 当其中一个元素已设置宽度时，其他元素（被设为table-cell）会占满剩下的宽度;
> - 如果只有一个table-cell元素，就算设置了宽度也会占满table元素的宽度;
> - 对设置了float、absolute的元素不起作用。且IE6、7不支持

## 方法七：父容器设置 display：flex；right部分设置 flex：1

```css
.parent{

  display: flex;

}

.right{

  flex: 1;

  background-color: brown;

}
```

### 参考：

> - https://www.jianshu.com/p/01747a08bcbf
> - https://blog.csdn.net/shuidinaozhongyan/article/details/73274651