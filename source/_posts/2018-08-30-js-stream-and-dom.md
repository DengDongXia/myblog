---
layout: post
title: js 事件流和事件处理程序（DOM 标准事件模型）
date: 2018-08-30
tags:
- js
categories: js
---

## 一、基本概念

1. 事件：文档或浏览器窗口中发生特定交互瞬间

2. 事件流：描述从浏览器中接收事件的顺序

IE的事件流：事件冒泡流
Netscape Communicator的事件流：事件捕获流

3. 事件冒泡：从触发事件的最具体元素开始，逐级向上传播到document对象

注：不是所有事件都支持冒泡事件；比如：鼠标事件（mouserleave, mouseenter等），焦点事件（blur, focus等），UI事件（load, unload, abort, error, scroll, resize等）
阻止事件冒泡的方式：（1）event.stopPropagation(); （2）低版本IE取消冒泡：event.cancelBubble(); （3）return false； 
注：第三种阻止方式，在取消事件冒泡事件的同时，也阻止了浏览器默认事件（event.preventDefault(); 低版本IE:window.event.returnValue = false; ）


4. 事件捕获：从document对象开始，事件沿DOM树逐级向下传播到具体目标

其用意：在事件到达预订目标事件之前捕获它

5. DOM事件流

包括三个阶段：事件捕获阶段->处于目标阶段（事件源target）->事件冒泡阶段
注： event.target 取到触发事件的源头；event.currentTarget 取到当前绑定事件的元素；

6. 事件处理程序：即响应某个事件的函数



## 二、 四种绑定事件方法：

1. HTML事件处理程序：即直接在html标签上绑定以"on"开头的事件

eg:
```html
<div id = "" onclick = "function() {}"></div>
```

> 缺点：
> - 存在时差问题：有可能要调用的函数还没有加载完，用户就触发事件，从而导致错误
> - js与html代码紧密耦合：修改代码时很麻烦

2. DOM0级事件处理程序：取得要操作的对象的引用，直接绑定on事件

eg:
```javascript
obj.onclick = function() {
    //处理内容
}
```

> 注：删除事件直接将处理事件的值设置为null; obj.onclick=null;

3. DOM2事件处理程序：使用addEventListener()进行事件监听

该方法接受的参数：要处理的事件名(不带on开头)、作为事件处理程序的函数和一个布尔值

> 布尔值表示：
> - true:表示在事件捕获阶段调用事件处理程序；
> - false:表示在事件冒泡阶段调用事件处理程序；

eg: 
```javascript
obj.addEventListener("click", function() {
    //处理内容
}, false);
```

> - 优势：可以添加多个事件处理程序，事件触发顺序从上往下执行
> - 删除事件：使用removeEventListener(), 传入与添加时相同的参数
> - 注意：匿名函数无法移除

4. IE事件处理程序：

attachEvent()添加、detachEvent()移除

参数：事件处理程序名称（带on开头）和事件处理程序函数

eg:
```javascript
obj.attachEvent("onclick", function() {
    //处理内容
});
```

> 1. 与DOM0级方法的区别：this的作用域不一样
> 2. 在DOM0中：事件处理程序会在其所属元素的作用域内运行
> 3. 在attachEvent()方法内：事件处理程序在全局作用域运行，this等于window
> 4. 当绑定多个事件时，事件触发顺序从下往上执行

#### 总结：

跨浏览器的事件处理程序，为保证处理事件的代码在不同浏览器下一致执行，只需关注冒泡阶段，可通过封装一个对象，进行方法调用。

参考书籍：

> - 《javaScript 高级程序设计》

