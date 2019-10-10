---
layout: post
title: css实现垂直居中的几种方式（布局常用）
date: 2018-05-21
tags:
- css
- layout
categories: css
---

## 前提：

html模板：
```html

<div class="parent">

<div class="content">内容垂直居中</div>

</div>
```

> 声明：以下示例，主要实现垂直居中的样式代码为加粗的部分

### 一、对单行元素进行垂直居中时

1. 可设置该行内元素的父元素的height与line-heigth的值相等，让行内元素垂直居中

2. 针对行内元素，可通过设置vertical-align: middle;以及line-height进行垂直居中

### 二、对文本进行垂直居中

1. 针对文本，通过display:flex;配合align-items和justify-content实现文本居中
```css

.content{

  display: flex;

  align-items: center;

  justify-content: center;

}
```

### 三、对已知高度块级元素进行垂直居中

1. 绝对定位，配合top:50%和负margin-top(元素高度一半)进行垂直居中

```css

.content{

  position: absolute;

  top: 50%;

  left: 50%;

  margin-top: -10em; /* 为元素height/2 */

  margin-left: -10em;

  width: 20em;

  height: 20em;

  background-color: aqua;

}
```

2. 绝对定位，配合top:0;bottom:0;和margin:auto进行垂直居中

```css
.content{

  position: absolute;

  margin：auto；

  top: 0;

  bottom: 0;

  left: 0;

  right: 0;

  height: 200px; /*要求指明元素高度*/

  background-color: aqua;

}
```

3. 设置position:absolute;和calc()函数实现垂直居中

```css

.content{

  position: absolute;

  top:calc(50% - 10em); /*calc(50% - 元素本身高度一半)*/

  left: calc(50% - 20em); /*注意使用时减号间有空格*/

  width: 40em;

  height: 20em;

  background-color: aqua;

}
```

4. 使用浮动float实现元素垂直居中

原理：通过在要进行垂直居中的元素a前面添加一个无内容的元素，并将该无内容元素的高设置为50%，在利用clear:botn清除浮动，则元素a相对于父元素来说是垂直居中。

html如下：

```html
<div class="parent">

  <div class="float"></div>

  <div class="content">

    <div><span>内容垂直居中内容垂直居中内容容垂居中</span></div>

  </div>

</div>
```

css如下：
```css

.parent{

  height: 500px;

  background-color: red;

}
/**添加的辅助元素*/
.float{

  height: 50%;

}

.content{

  clear: both;

  background-color: aqua;

}
```

### 四、对未知高度块级元素进行垂直居中

1. 设置position：absolute；和transform:traslate(x,y)实现水平垂直居中
```css
.content{

  position: absolute;

  margin：auto；

  top: 50%;

  left: 50%;

  transform:translate(-50%,-50%); /*针对元素本身向左以及向上移动50%*/

  background-color: aqua;

}
```

2. 居于视口单位的解决方案：

可通过使用margin-top: 50vh;配合transform:translateY(-50%);实现视口居中

```css

.content{

  width: 18em;

  margin-top: 50vh; /*50vh表示视口高度的50%*/

  transform: translateY(-50%); /*相对元素自身向上移动50%*/

  background-color: aqua;

}
```

3. 通过display:table-cell和vertical-align:middle;实现垂直居中

```css
.parent{

  display: table;

  width: 50px; /*建议设置宽高，以便于查看效果*/

  height: 500px;

}

.content{

  display: table-cell;

  vertical-align: middle;

  background-color: aqua;

}
```

4. 基于flex的解决方案：

```css
.parent{

  display: flex;

  background-color: beige;

}

.content{

  margin: auto; /*自动相对于父元素水平垂直居中*/

  background-color: aqua;

}
```



