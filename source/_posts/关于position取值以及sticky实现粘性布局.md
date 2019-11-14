---
title: 关于position取值以及sticky实现粘性布局
date: 2018-08-30 21:22:05
categories:
- css
tags:
- position
- sticky
---
## position可取值：

- static：常规流，top，right，bottom，left，z-index属性无效
- relative：table-*-group、table-row、table-column、table-cell、table-caption元素无效
- absolute：不为元素预留空间，通过指定元素相对于最近的非 static 定位祖先元素的偏移，来确定元素位置。绝对定位的元素可以设置外边距（margins），且不会与其他边距合并
/* 全局值 */
- inherit
- initial
- unset
/* 新增 */
- sticky(拥有-webkit- 内核浏览器才支持)：实现粘性布局

## position: sticky
&emsp;&emsp;元素先按照正常流定位，当元素滚动到设定的阈值时，元素的显示效果会变成position:fixed效果相同

### 设置中间元素如下：

position: sticky;
top: 20px; //元素滚动到距离顶部为20px时，停止滚动（效果类似position: fixed; top: 20px;）

![](/images/2019-11-14-19-29-50.gif)

兼容性：

![](/images/2019-11-14-19-29-03.png)

### 生效规则：

- 1、需设置top/rigth/bottom/left中的一个值，粘性定位才生效，否则效过与相对定位相同
- 2、被设置为position: sticky的元素的任意父节点的overflow须是visible:
如果 position:sticky 元素的任意父节点定位设置为 overflow:hidden，则父容器无法进行滚动，所以 position:sticky 元素也不会有滚动然后固定的情况。
如果 position:sticky 元素的任意父节点定位设置为 position:relative | absolute | fixed，则元素相对父元素进行定位，而不会相对 viewprot 定位。
- 3、元素表现为：relative还是fixed是有设定的阈值决定，比如说上面的top值

## 参考文章

- [position](https://developer.mozilla.org/zh-CN/docs/Web/CSS/position)
- [使用 position:sticky 实现粘性布局](http://www.cnblogs.com/coco1s/p/6402723.html)