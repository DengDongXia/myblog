---
layout: post
title: css实现水平居中的几种方式
date: 2018-05-13
tags:
- css
- layout
categories: css
---

### 一、对于行内元素：

```css
text-align:center;
```

### 二、对于确定宽度的块级元素：

1. margin和width实现水平居中

常用(前提：已设置width值)：margin-left:auto; margin-right:auto;

2. 绝对定位和margin-left: -(宽度值/2)实现水平居中

固定宽度块级元素水平居中，通过使用绝对定位，以及设置元素margin-left为其宽度的一半

```css
.content{

  width: 200px;

  position: absolute;

  left: 50%;

  margin-left: -100px; // 该元素宽度的一半，即100px

  background-color: aqua;

}
```

3. position:absolute + （left=0+top=0+right=0+bottom=0） + margin:auto

```css
.content{

  position: absolute;

  width: 200px;

  top: 0;

  right: 0;

  bottom: 0;

  left: 0;

  margin: auto;

}
```


### 三、对于未知宽度的块级元素：

1. table标签配合margin左右auto实现水平居中

使用table标签（或直接将块级元素设值为display:table），再通过给该标签添加左右margin为auto

2. inline-block实现水平居中方法

display：inline-block;（或display:inline）和text-align:center;实现水平居中

> 存在问题：需额外处理inline-block的浏览器兼容性（解决inline-block元素的空白间距）

3. 绝对定位实现水平居中

绝对定位+transform，translateX可以移动本省元素的50%
```css
.content{

  position: absolute;

  left: 50%;

  transform: translateX(-50%); /* 移动元素本身50% */

  background: aqua;

}
```

4. 相对定位实现水平居中

用float或者display把父元素变成行内块状元素

```css
.contentParent{

  display: inline-block; /* 把父元素转化为行内块状元素 */

  /*float: left; 把父元素转化为行内块状元素 */

  position: relative;

  left: 50%;

}

/*目标元素*/

.content{

  position: relative;

  right: 50%;

  background-color:aqua;

}
```

5. CSS3的flex实现水平居中方法，法一

```css
.contentParent{

  display: flex;

  flex-direction: column;

}

.content{

  align-self:center;

}
```

6. CSS3的flex实现水平居中方法，法二

```css
.contentParent{

  display: flex;

}

.content{

  margin: auto;

}
```

7. CSS3的fit-content配合左右margin为auto实现水平居中方法

```css
.content{

  width: fit-content;

  margin-left: auto;

  margin-right: auto;

}
```

### 参考文章：

> - https://www.w3cplus.com/css/elements-horizontally-center-with-css.html

> - http://blog.sina.com.cn/s/blog_c112a2980102xt3y.html