---
title: outline 与 clip 实现自动填满屏幕剩余空间的应用技巧 -- 解决页面底部留白问题
date: 2019-11-07 20:19:17
tags:
- css
- outline
- clip
---

## 场景

> 前言：前端开发一个网站时，可能经常会遇到这么一个需求，每个页面都会添加一个底部并附上网站的信息等等。

![](/images/2019-11-13-19-55-42.png)

然而，我们也经常会遇到以下问题：
#### 问题描述：页面主内容很少时（包括底部在内不足一屏），可能会出现高度不足导致底部下面留白。

    
```html
<div class="hello">
    <header>头部</header>
    <main>主体内容</main>
<footer>底部</footer>
</div>
```

#### 一、常规页面底部css写法以及存在问题

```css
header {
  height: 50px;
  background: #5d6156;
  color: #fff;
}
main {
  height: 500px;
}

footer {
  background: #212020;
  color: #fff;
  padding: 40px;
}
```
效果：整体页面高度不够时，页面底部留白
![](/images/2019-11-13-19-56-00.png)

所以，今天我是来推荐一个好用的底部填充的css实现方式的

#### 二、使用 outline 与 clip 实现自动填满屏幕剩余空间，从而覆盖整个底部

#### 解决方法：

&emsp;&emsp;设置一个超大轮廓范围的outline（该属性不占据任何空间）属性，保证完整的覆盖整个屏幕。

&emsp;&emsp;但由outline 无法指定方位，只能被动地四周拓展。但我们只是希望达到底部下面不留白，并不想要上面也被覆盖。所以需要使用一种方式将底部上边的隐藏或去除。除了通过改变主体内容的层叠顺序，达到覆盖的目的之外，还可以使用clip裁剪策略。同时已上边缘和左边缘为界对底部进行裁剪，就可以达到我们想要的目的。

```css
.footer > p {
    position: absolute;
    left: 0;
    right: 0;
    text-align: center;
    padding: 15px 0;
    background-color: black;
    outline: 9999px solid black;
    clip: rect(0, 9999px, 9999px, 0);
}
```

效果：设置outline 为很大的值，盖满整个屏幕，再利用 clip 以上边缘和左边缘为界进行裁切

![](/images/2019-11-13-19-56-13.png)

#### outline 兼容性

![](/images/2019-11-13-19-56-23.png)

#### clip 兼容性

![](/images/2019-11-13-19-56-32.png)

#### 综上，除了ie 6-7, outline 与 clip 的兼容性都达到90%以上，感觉还是很不错，所以这个算是一个很不错的实现自动填满屏幕剩余空间的应用技巧

#### 参考
- 《css 世界》