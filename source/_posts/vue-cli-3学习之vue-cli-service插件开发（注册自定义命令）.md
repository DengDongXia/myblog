---
title: vue-cli 3学习之vue-cli-service插件开发（注册自定义命令）
date: 2019-02-25 18:31:12
tags:
- vue-cli 3
- vue-cli-service
- plugin
categories: 
- 构建
- vue-cli3
---
## 前言

提到 vue-cli，[官方文档](https://link.juejin.im/?target=https%3A%2F%2Fgithub.com%2Fvuejs%2Fvue-cli%2Fblob%2Fmaster%2FREADME.md)对其的介绍如下：
> A simple CLI for scaffolding Vue.js projects. 一个简单的Vue.js工程命令行脚手架工具。

说白了，`vue-cli` 其实就是一个基于`webpack`构建，可以让用户快速初始化一个项目的工具。

**基本原理**: `vue-cli`并非从无到有地凭空生成一个项目，而是通过下载/拉取(通过`download-git-repo`工具)已有的工程到本地，完成生成项目的工作。

> - 注意安装`vue-cli 3`前，如若已安装旧版本`vue-cli`，需先卸载旧版本`vue-cli`，再重新安装vue-cli 3
> - `node`版本要求:  [安装](https://link.juejin.im/?target=http%3A%2F%2Fnodejs.cn%2Fdownload%2F) Node.js8.9 或更高版本

这次我分享的主要主要内容是 **`vue-cli 3`**,当然不是全面的介绍，只是将我这次运营系统工作流改造中遇到比较深刻的点进行一番简单介绍

而对于根据我个人对`vue-cli 3`的认识，我将需掌握的内容概括为四个部分:**`Vue.config.js`**、**`Vue-cli-service`**、**`插件`**、**`UI`**。

## 概括：

下面这张图是我对vue-cli 3 知识点进行了一个简单的概括

比较常接触到`vue`一些基本的命令，比如说：
- vue create: 快速初始化一个项目
- vue serve：为某个原型（如：单独一个`vue`文件）快速启动一个服务（要求全局安装`@vue/cli-service-global`）
- vue ui: 通过图形化界面创建或管理界面

![](https://user-gold-cdn.xitu.io/2019/2/25/169248590b2fbadc?w=1255&h=585&f=png&s=181919)

## 接下来谈一谈 `Vue.config.js`

我觉得对于一个普通的项目的工作流构建来说，[配置](https://note.youdao.com/ynoteshare1/index.html?id=44b35da84f7d6b95153d6f7fcebbd264&type=note#/)`vue.config.js`足够了

而里面比较特别和常用的主要是以下这些：除了 **`publicPath`**、 **`outputDir`**、 **`filenameHashing`**、 **`pages`** 等这些常用字段外，我觉得还需要提一下的是 **`configureWebpack`** 字段和 **`chainWebpack`** 字段

我对这两个字段的理解是
- configureWebpack 是让用户字段写一个配置对象，其内部再通过[`webpack-merge`](https://github.com/survivejs/webpack-merge)将原有对象和用户配置的对象进行合并
- chainWebpack 是通过利用[`webpack-chain`](https://github.com/neutrinojs/webpack-chain)提供的链式调用方法去直接修改原有的配置对象
![在这里插入图片描述](https://user-gold-cdn.xitu.io/2019/2/25/16924858feec03b6?w=631&h=595&f=png&s=64132)

> 对于 `vue-cli 3`来说，它还存在一个比较特别的模块，就是它提供了自定义插件的功能

## 插件

说到`vue-cli 3` 的插件，其实可以将其分为两种情况：

1. 一种是`vue-cli`插件,这种情况一般是对外的，主要是开发一些`npm`包，并将其发布到`npm`上；

2. 另一种就是`service 插件`, 也就是我这次工作流改造中用到的主要内容，其主要包括內建命令和配置模块，而这两部分的内容都是在调用`vue-cli-service`命令时才会触发;因此，使用到`service `插件的用户，需要安装一下`vue-cli-service`。

- Cli插件（`Generator`和`Prompts`）：为`@vue/cli`项目添加额外特性的 npm 包
- **service插件**（內建命令和配置模块）：会在一个`Service`实例被创建时自动加载——比如每次`vue-cli-service`命令在项目中被调用时。

##### Vue-cli-service

> 1. 內建命令
>> 该功能的实现主要是调用了`api.registerCommand`这个方法向vue-cli-service内部注入额外的命令，`vue-cli 3`提供的`vue-cli-service serve`和`vue-cli-service build`等命令的实现也是利用了这个方法。
> 2. 配置模块
>> 该功能的实现则是利用的`api.chainWebpack`和`api.configureWebpack`方法对`webpack`配置进行修改。
> 3. 注：稍后我会在文章的下面给出一个自定义命令的 demo

```javascript
// 可以通过下面这个命令查看所有命令，从而确认自定义的命令是否创建成功
npx vue-cli-service help
```
![在这里插入图片描述](https://user-gold-cdn.xitu.io/2019/2/25/16924858fd37c5ee?w=528&h=334&f=png&s=33989)

> 然后接下来看一看具体实操例子

## 需求场景

1. 支持`watch`和`build`命令，并满足以下功能
2. 同时支持`--key`命令读取`.json`文件中的`key`来进行构建，并支持多个`key`
3. 同时支持`.json`配置文件，可以指定`entry`路径和`output`路径

需求示例：
```bash
# watch 监听某个入口的文件变动并编译
$ node ./scripts/Bundler.js watch --key=[your_key]

# build 打包编译某个入口的文件(生产模式)
$ node ./scripts/Bundler.js build --key=[your_key]
```

这对第三点，假设项目根目录下有 `Views.json` 文件，形如：

```json
{
  "basePath": "resources/assets/js/views",
  "destBasePath": "public/js/res",
  "entry": {
    "page": { // 入口的唯一命名，打包和开发时要用到的 key 值
      "name": "page", // 入口文件的文件名(例如 queryDetail.js)
      "srcPath": "job/template/page", // JS 入口源文件所在的文件夹的根目录
      "output": "page" // 输出文件的名称
      "description": "页面功能描述", // 页面功能描述，不启任何作用
    }
  }
}
```

> 然而我为什么没有直接使用`vue-cli-service`提供的`serve`和`build`命令呢？
>> 原因如下：
>> 对于`watch`和`build`命令，其实他们除了要求具备`vue-cli-service`提供的`serve`和`build`命令效果外，还得让用户通过`--key`传入打包入口，而`vue-cli-service`提供的这些命令是其内部写好了的，我们无法对其内部进行修改，所以为了实现这个需求，我们需要进行自定义命令。


解决方式：

要实现 `watch` 和 `build` 功能，其实在 `vue-cli 3` 中 `vue-cli-service` 有提供了相对应的 `api` 给用户进行调用，也就是注册自定义的命令，实际上就是向 `vue-cli-service` 注入额外的命令。

##  注册命令的第一种方式 ---- 使用 vue-cli-service 插件

以注册`watch`命令为例，做一个小`demo`：

> [demo github 地址](https://github.com/DengDongXia/plugin-demo)

1. 新建一个项目
```javascript
// 首先进行 vue-cli 安装
npm i -g @vue/cli
// 查看是否安装成功，看到版本号就是安装成功了
vue -V
// 新建一个项目
vue create 项目名
```
2. 针对已有项目使用 `vue-cli-service`

安装`vue-cli-service`：
```javascript
 // 进行 vue-cli-service 安装
 npm i -D @vue/cli-service
```

3. 在`package.json`中的`vuePlugins`对象中的`service`字段引入相应的js文件（用于注册`watch 命令`的文件, 假设该文件名为：`vue-cli-plugin-watch.js`，并放在根目录）
```json
 // package.json 中
 // 注册命令的 js 文件名为 vue-cli-plugin-watch.js
 {
   "scripts": {
    "watch": "vue-cli-service watch"
   },
   "vuePlugins": {
      "service": [
        "./vue-cli-plugin-watch"
      ]
   }
 }
```

4. `vue-cli-plugin-watch.js` 代码如下
```javascript
module.exports = (api, projectOptions) => {
  api.registerCommand('watch', {
  	description: 'watch plugin for vue cli 3',
  	usage: 'vue-cli-service watch',
  	options: {

    }
  }, (args) => {
    console.log('watch 命令注册成功')
  })
}
```

5. 在终端输入`npm run watch`，看到终端输出“`watch`命令注册成功”后，恭喜`watch`命令基本注册完成啦。

可以通过运行`npx vue-cli-service help`查看vue-cli-service下面的所有命令，可以看到新增加了一个`watch`命令
![在这里插入图片描述](https://user-gold-cdn.xitu.io/2019/2/25/16924858fd78e1ee?w=672&h=237&f=png&s=31921)

6. 补充：另外，对于一个插件的使用，我们可以选择性的让用户进行一些设置；通过让用户在`vue.config.js`中的`pluginOptions`向插件进行传参数

在项目根目录创建`vue.config.js`文件

```javascript
// vue.config.js相关代码
module.exports = {
  pluginOptions: {
    watch: {
      // vue-cli-plugin-watch 插件可以作为 `projectOptions.pluginOptions.watch` 访问这些选项，其他插件也可以拿到
      param: '传参数给內建插件'
    }
  }
}
```
修改`vue-cli-plugin-watch.js` 代码如下
```javascript
module.exports = (api, projectOptions) => {
  api.registerCommand('watch', {
  	description: 'watch plugin for vue cli 3',
  	usage: 'vue-cli-service watch',
  	options: {}
  }, (args) => {
    // 输出传入的参数
    console.log('watch 命令注册成功')
    // projectOptions 拿到的是 vue.config.js 文件暴露出来的配置
    console.log(projectOptions.pluginOptions.watch.param)
    // 通过终端传进来的参数
    const key = args.key ? args.key : process.env.npm_config_key
    console.log(`--key = ${key}`)
  })
}
```

> 运行`npm run watch`命令，可见终端输出了‘传参数给內建插件’，即通过vue-config.js传参成功
> 运行`npm run watch `命令，可见终端输出了‘传参数给內建插件’，即传参成功

其实再去看看`build`命令的实现源码，其实和上面watch的实现是差不多的

Vue-cli-service build 命令源码
![在这里插入图片描述](https://user-gold-cdn.xitu.io/2019/2/25/16924858fed428d6?w=836&h=383&f=png&s=79054)
注册命令后，就可以通过调用 api.resolveWebpackConfig() 取回解析好的 webpack 配置。每次调用都会新生成一个 webpack 配置用来在需要时进一步修改。
```javascript
module.exports = api => {
  api.registerCommand('my-build', args => {
    const configA = api.resolveWebpackConfig()
    const configB = api.resolveWebpackConfig()

    // 针对不同的目的修改 `configA` 和 `configB`...
  })
}
```
也可以通过调用 api.resolveChainableWebpackConfig() 获得一个新生成的链式配置
```javascript
api.registerCommand('my-build', args => {
  const configA = api.resolveChainableWebpackConfig()
  const configB = api.resolveChainableWebpackConfig()

  // 针对不同的目的链式修改 `configA` 和 `configB`...

  const finalConfigA = configA.toConfig()
  const finalConfigB = configB.toConfig()
})
```

> 再看回operation的项目需求，要求实现`watch`和`build`,`watch`实现还好说，但是考虑到直接使用`vue-cli-service`的内建命令模块实现`build`会将其原有的命令进行覆盖，这种操作不太理想，所以改用了另外一种实现方式:使用 **`commander`** 实现注册命令。

## 注册命令的第二方式 ---- [Commander](http://tj.github.io/commander.js/)

- 定制自己的命令`watch`

> [`demo github`源码地址](https://github.com/DengDongXia/command-plugin-demo)

主要实现代码如下：
![在这里插入图片描述](https://user-gold-cdn.xitu.io/2019/2/25/16924858fee1255e?w=478&h=189&f=png&s=17798)
`demo`具体步骤：
1. 初始化一个项目
2. 在根目录下创建一个`script`文件夹，在该文件夹目录下新建一个用于实现注册命令的js文件，我在这创建的文件名为`commandar-watch.js`

```javascript
// 安装 Commander
npm install Commander -D
```
```javascript
// commandar-watch 具体实现代码
// 注：可同时注册多个命令

const program = require('commander')

program.version('0.1.0')
  .option('-k, --key [type]', 'key to build')

program
  .command('watch')
  .description('run the given remote command')
  .action(function(){
    console.log('watch 命令注册成功')

    /**
     * 用户传进来的 key 值
     * yarn 运行命令时，可通过 program.key 获取到用户传进来的 key 值
     * npm 运行命令时，需通过 process.env.npm_config_key 获取到用户传进来的 key 值
     */
    const key = program.key ? program.key : process.env.npm_config_key
    console.log(key)
})

program.parse(process.argv)
```
3. 在`package.json`文件中`scripts`的`watch`中，使用`node`执行`commandar-watch.js`的`watch`命令
```javascript
{
"scripts": {
    "watch": "node ./script/commandar-watch.js watch",
  },
}
```
4. 在终端执行`npm run watch`命令，看到终端输出‘watch 命令注册成功’后，恭喜`watch`命令注册成功啦。当然，你也可以传入`--key`的值，如：`npm run watch --key=用户传的key值`，可见到终端输出‘用户传的key值’，证明已经获取到用户传的参数啦。

## 再谈谈项目中 build 和 watch 的不同点
其实这两个命令主要用到的配置还是Vue-cli 3提供的基本配置，在再次基础上针对不同页面特别设置一些配置，比如说针对某些页面需要进行css提取，还有不同页面设置不同的`entry`和`output`等等。而着这两个命令的主要区别就是Watch 字段、不同mode；对于是否压缩打包，我通过设置不同的环境模式从而设置文件是否需要打包（webpack 4提供了这个功能）；至于是否需要监听文件的改变功能的实现，我借鉴了`vue-cli-service build`命令的`--watch`实现方式。

vue-cli-service build --watch ，核心代码 是获取webpack 配置，并将配置对象的watch字段的值设置为true。![在这里插入图片描述](https://user-gold-cdn.xitu.io/2019/2/25/1692485940a6432f?w=399&h=126&f=png&s=10045)
> 注: `vue-cli-service`中的`build`命令`--watch`的实现[源码地址](https://github.com/vuejs/vue-cli/blob/dev/packages/%40vue/cli-service/lib/commands/build/index.js)


##### Public 文件夹
主要用来放置一些无需经过webpack处理的静态资源或模板等等。其目录下的内容会被直接拷贝到构建输出目录下
关于存在原因：index.html模板，动态引入图片，引入不兼容模块

> 实现源码
![在这里插入图片描述](https://user-gold-cdn.xitu.io/2019/2/25/169248592af6f0b1?w=576&h=332&f=png&s=102642)
## 推荐使用 inspect

审查 vue-cli 项目的 webpack 配置，可以很方便将查看使用 `vue-cli 3`配置后，其具体的 `webpack`配置
![在这里插入图片描述](https://user-gold-cdn.xitu.io/2019/2/25/1692485929e1d5c1?w=323&h=366&f=png&s=96749)

## 结语
针对这次分享的内容，虽然说我侧重分享了插件部分的内容，但是并不意味着`vue-cli 3` 内容只有这些，只是我这次开发中遇到比较多的模块就是`service`插件这一块，所以就特别提了一下，它还有其他的内容比如说ui那一块等等；

体会：
在开发过程中，当遇到，对api不熟悉，造成无法很好的运用好某些`api`的情况时，其实可以考虑去研究研究一下源码，会发现事情变得简单，其实在`vue-cli` 的官网里面也有提到配合源码去学习`api`，这才能真正灵活的使用对应的功能等等。
在这个项目里面，我就是在一边学习一边实现需求。还有，考虑问题要全方面的想，多测试一些页面。比如说`npm`与`yarn`在终端中的传参方式不一样，导致代码中获取的`key`值方式也不一样这个问题，就是得多测试才会发现。还有要多请教一些前辈，你会发现有时候经验对于开发方式和开发效率来说，真的很重要。

参考文章：

> - [深入认识 vue-cli：能做的不仅仅是初始化 vue 工程](https://juejin.im/post/5c5193b1f265da614a3ae13b)

拓展文章：

> - [vue-cli 3学习知识点总结](http://note.youdao.com/noteshare?id=87e9249155ef6da0818b4f5afff91217&sub=4DB4470F37CE43EAA54EA75DBA3A883C)
> - [vue.config.js 基本配置](http://note.youdao.com/noteshare?id=44b35da84f7d6b95153d6f7fcebbd264&sub=F186F25C20024E70962F3543C5D36B60)
> - [vue-cli 插件](http://note.youdao.com/noteshare?id=248eb7265f22f5524e58d1245e57008d&sub=CCF6EC6EFCD8415FBDAA162698E3A853)
