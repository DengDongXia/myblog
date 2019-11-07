---
title: 前端构建踩坑 --- babel-loader 对已编译的脚本进行再编译从而导致文件执行出错
date: 2019-11-06 20:38:13
tags: 
- BuildTool
- 构建配置
- babel-loader
- vue-cli 3
categories: 
- 构建
- babel-loader
---
## 前言

#### 问题描述： babel-loader 对已编译的脚本进行再编译从而导致文件执行出错。

&emsp;&emsp;因为打算开发一个小组件库，但是要对已经编译完成的组件库的脚本进行一遍完整的测试。所以，本人就很自然的启动一个项目，引入已经编译好的组件库脚本，想着大展伸手，结果。。。

![](https://user-gold-cdn.xitu.io/2019/11/7/16e43654c6f2a722?w=225&h=225&f=png&s=58679)

&emsp;&emsp;遇到了一大堆脑瓜子疼的警告`"export 'default' (imported as 'a') was not found in 'xxx' "`，或乱七八糟的报错...（捂脸哭.jpeg）。


![](https://user-gold-cdn.xitu.io/2019/11/7/16e4365783b6604f?w=980&h=290&f=png&s=44875)


![](https://user-gold-cdn.xitu.io/2019/11/7/16e4365a9f458984?w=1784&h=490&f=png&s=173638)


- 原本我要引入的脚本`a.umd.js`是使用了ES6 模块的写法，将对应的模块export default 出来的。
- 再将组件库发包到 npm上，并通过npm install 去将该组件库安装到node_modules对应的目录中。
- 然后使用import a from 'XXX'去引入，结果这个组件库是可以正常使用的。
- 但是，当直接在本地(不是在node_modules)引入该脚本或在直接在package.json中将该组件库作为本底依赖安装也不行时，就会出现上面提示的警告（或报错）。

&emsp;&emsp;原本想着只有在node_modules中才能正常使用，好，为了调试，我选择直接将该脚本（a.umd.js）作为本地依赖引入，因此，我就很兴奋去尝试了。。。步骤是这样的：
```js
// 在package.json直接通过通过本地依赖的方式引入脚本a.umd.js
{
  "name": "baz",
  "dependencies": {
    "a": "file:./config/a.umd.js"
  }
}
```

嗯，很顺利`npm i`完成，然鹅。


![](https://user-gold-cdn.xitu.io/2019/11/7/16e436601a87080c?w=688&h=376&f=png&s=334847)

直接通过本地依赖的方式依旧存在以上的问题。


### 那么为什么通过`npm i`发布到 npm 的脚本`a.umd.js`能在 node_modules 正常使用而通过本地依赖的方式安装 node_modules 以及直接引用却不行？

![](https://user-gold-cdn.xitu.io/2019/11/7/16e43666f2eb7c00?w=272&h=185&f=png&s=21975)

## 后来

经过网上查资料以及分析项目，终于发现了这么一个事情。。。


![](https://user-gold-cdn.xitu.io/2019/11/7/16e4366b86403ca7?w=1892&h=300&f=png&s=49352)


&emsp;&emsp;嗯，是的，babel-loader 对`a.umd.js`这个脚本，再次进行了编译。。。导致原本编译好的可以正常使用的脚本再次进一步被编译了，所以才会报错`"export 'default' (imported as 'a') was not found in 'xxx' "`。


![](https://user-gold-cdn.xitu.io/2019/11/7/16e436712b008411?w=762&h=744&f=png&s=104510)
## 因此

&emsp;&emsp;为了能顺利地在本地调试组件库的脚本a.umd.js，我只能选择在引入该脚本 a.umd.js调试的项目中进行 babel-loader 的配置，将 babel-loader 对 a.umd.js 的编译进行exclude。即babel-loader 不编译名字中包含`a.umd`字符的文件。

- 如果直接使用webpack配置，则配置如下：
```js
module: {
  rules: [
    {
      test: /\.m?js$/,
      exclude: /a.umd/,
      use: {
        loader: 'babel-loader',
        options: {
          presets: ['@babel/preset-env']
        }
      }
    }
  ]
}
```

- 如果是使用vue-cli，则vue.config.js配置如下：
```js
chainWebpack: (config, isServer) => {
// config 是 ChainableConfig 的一个实例    
config.module.rule('js').use('babel-loader')
.loader('babel-loader').tap(options => {
    options.exclude = /a.umd/
    // 修改它的选项...
    return options
  })
},
```

从过以上的方式就可以避免babel-loader 对已经编译的文件进行二次编译

## 所以，综上所述

- vue-cli3 中 `babel-loader` 的配置是不对 node_moudules 文件夹中的文件进行编译，所以当直接将脚本放在 node_modules 或直接通过 npm 远程 install 都能正常使用。
- 而当引入的脚本不在 node_modules 中或通过本地依赖的方式安装到 node_modules 都会被babel-loader二次编译（当然设置了exclude的除外）
