---
layout: post
title: vue-cli 3 知识点整理
date: 2019-02-25
tags:
- BuildTool
---

## 关于指南
>笔记简单整理如下：

### 一、介绍
![](https://img-blog.csdnimg.cn/20190215193934278.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdkb25neGlh,size_16,color_FFFFFF,t_70)
![](https://img-blog.csdnimg.cn/20190215194018429.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdkb25neGlh,size_16,color_FFFFFF,t_70)

### 二、安装

1. 前提：

（1）node 版本要求：8.9或更高版本

（2）如若已安装旧版本vue-cli ，则将其卸载，重新安装@vue-cli

2. 安装命令：

```javascript

// 局部安装
npm i @vue/cli  或 yarn add @vue/cli

//全局安装
npm i -g @vue/cli  或 yarn add -g @vue/cli

```

3. 检查是否安装成功 (全局安装时可用以下命令检查)

```javascript
vue --version
```

###  三、基础

1. 搭建基本项目

（1）快速构建命令 vue serve 和 vue build

>前提：全局安装 @vue/cli-service-global 依赖

```javascript
npm install -g @vue/cli-service-global
```

（2）vue serve 命令的选项

```javascript
Usage: serve [options] [entry]

在开发环境模式下零配置为 .js 或 .vue 文件启动一个服务器

Options:
  -o, --open  打开浏览器
  -c, --copy  将本地 URL 复制到剪切板
  -h, --help  输出用法信息
```

（3）vue build 命令选项：

```javascript
Usage: build [options] [entry]

在生产环境模式下零配置构建一个 .js 或 .vue 文件

Options:

  -t, --target <target>  构建目标 (app | lib | wc | wc-async, 默认值：app)
  -n, --name <name>      库的名字或 Web Components 组件的名字 (默认值：入口文件名)
  -d, --dest <dir>       输出目录 (默认值：dist)
  -h, --help             输出用法信息
```

（4）快速创建一个项目命令

```javascript
vue create
```

2. 插件和preset

- 插件的注册与声明
- Preset
- 一个 Vue CLI preset 是一个包含创建新项目所需预定义选项和插件的 JSON 对象，让用户无需在命令提示中选择它们。

3. CLI服务

（1）vue-cli-service serve 启动一个开发服务器并附带开箱即用的模块热加载
（可通过 devServer 配置开发服务器 ）

```javascript
 用法：vue-cli-service serve [options] [entry]

选项：
  --open    在服务器启动时打开浏览器
  --copy    在服务器启动时将 URL 复制到剪切版
  --mode    指定环境模式 (默认值：development)
  --host    指定 host (默认值：0.0.0.0)
  --port    指定 port (默认值：8080)
  --https   使用 https (默认值：false)
 ```

（2）vue-cli-service build

（ 在输出目录产生一个生产环境的包 ，自动进行代码压缩，vendor chunk splitting，内联chunk manifest 在 HTML 里。 ）

```javascript
用法：vue-cli-service build [options] [entry|pattern]

选项：
  --mode        指定环境模式 (默认值：production)
  --dest        指定输出目录 (默认值：dist)
  --modern      面向现代浏览器带自动回退地构建应用
  --target      app | lib | wc | wc-async (默认值：app)
  --name        库或 Web Components 模式下的名字 (默认值：package.json 中的 "name" 字段或入口文件名)
  --no-clean    在构建项目之前不清除目标目录
  --report      生成 report.html 以帮助分析包内容
  --report-json 生成 report.json 以帮助分析包内容
  --watch       监听文件变化
```

（3）vue-cli-service inspect

（审查 vue-cli 项目的 webpack 配置）

```javascript
用法：vue-cli-service inspect [options] [...paths]

选项：
  --mode    指定环境模式 (默认值：development)
```

4. 查看所有可用命令

```javascript
npx vue-cli-service help
```

5. 缓存和并行处理

（1）cache-loader：会默认为 Vue/Babel/TypeScript 编译开启。文件会缓存在 node_modules/.cache 中

注：如果你遇到了编译方面的问题，记得先删掉缓存目录之后再试试看。

（2）thread-loader

6. git Hook

### 四、开发

1. 浏览器兼容性

（1）browserslist（指定了项目的目标浏览器的范围）

（2）ployfill

（3）现代模式

2. HTML 和 静态资源

（1）index 文件

> public/index.html 文件是一个会被 html-webpack-plugin 处理的模板。在构建过程中，资源链接会被自动注入

不生成index:
```javascript
// vue.config.js
module.exports = {
  // 去掉文件名中的 hash
  filenameHashing: false,
  // 删除 HTML 相关的 webpack 插件
  chainWebpack: config => {
    config.plugins.delete('html')
    config.plugins.delete('preload')
    config.plugins.delete('prefetch')
  }
}
 ```

（2）插值

（3）preload
指定页面加载后很快会被用到的资源，通过 [@vue/preload-webpack-plugin](https://github.com/vuejs/preload-webpack-plugin) 进行注入

（4）Prefetch
是一种 resource hint，用来告诉浏览器在页面加载完成后，利用空闲时间提前获取用户未来可能会访问的内容。通过 [@vue/preload-webpack-plugin](https://github.com/vuejs/preload-webpack-plugin) 进行注入

（5）构建一个多页应用

（6）处理静态资源

> 静态资源处理方式

- 在 JavaScript 导入或在 template/CSS 中通过相对路径被引用。这类引用会被 webpack 处理。
- 放置在 public 目录下或通过绝对路径被引用。这类资源将会直接被拷贝，而不会经过 webpack 的处理。

> 从相对路径导入

- 使用相对路径引入资源，url 都会被解析成一个依赖模块
如：url(./image.png) 会被翻译为 require('./image.png')
- 通过 file-loader 用版本哈希值和正确的公共基础路径来决定最终的文件路径，再用 url-loader 将小于 4kb （可调整为其他值） 的资源内联，以减少 HTTP 请求的数量

> URL转换规则

- url 为绝对路径，路径被保留
- url 以 . 开头，按照从相对路径导入方式进行解析
- url 以 ~ 开头，任何内容都按照模块请求被解析，可引入 node 模块资源
- url 以 @ 开头，任何内容都按照模块请求被解析，它的用处在于 Vue CLI 默认会设置一个指向 <projectRoot>/src 的别名 @。(仅作用于模版中)

>	public 文件夹（不推荐，只是应急手段）

任何放置在 public 文件夹的静态资源都会被简单的复制，而不经过 webpack。你需要通过绝对路径来引用它们。
通过webpack 处理的好处:

- 脚本和样式表会被压缩且打包在一起，从而避免额外的网络请求。
- 文件丢失会直接在编译时报错，而不是到了用户端才产生 404 错误。

最终生成的文件名包含了内容哈希，因此你不必担心浏览器会缓存它们的老版本

> 何时使用 `public` 文件夹

- 你需要在构建输出中指定一个文件的名字（定义构建模板）。
- 你有上千个图片，需要动态引用它们的路径。
- 有些库可能和 webpack 不兼容，只能将其用一个独立的 `<script>` 标签引入。

3. `css` 相关

（1）引用静态资源
所有编译后的 `CSS` 都会通过 `css-loader` 来解析其中的 url() 引用，并将这些引用作为模块请求来处理

（2）预处理器

（3）自动化导入

如自动化导入文件(用于颜色、变量、mixin……)，你可以使用`style-resources-loader`

（4）`postCss`

（5）`css Modules`

- 通过< style module > 以开箱即用的方式在.vue文件里面使用

- JavaScript 中作为 `CSS Modules` 导入`CSS` 或其它预处理文件，该文件应该以 `.module.(css/|less/|sass/|scss/|styl) `结尾(设置 `vue.config.js` 中的 `css.modules`为 `true`：导入文件时，文件名无需带上`.module`; 同时，即使构建时，`import`的文件为`css`文件，但也会将其当做 `css Modules` 来打包)

- 关于 `css Modules` 模块的类名

默认的`css Modules` 模块的类名

![](https://img-blog.csdnimg.cn/20190215195459339.png)

自定义生成的css Modules 模块的类名

```javascript
// 可以通过 vue.config.js中的 css.loaderOptions.css 选项来实现
// 所有的 css-loader 选项在这里都是支持的，例如 localIdentName 和 camelCase
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      css: {
        localIdentName: '[name]-[hash]',
        camelCase: 'only'
      }
    }
  }
}
```

- 向预处理器 Loader 传递选项

> 提示: 这样做比使用 chainWebpack 手动指定 loader 更推荐，因为这些选项需要应用在使用了相应 loader 的多个地方。

```javascript
// vue.config.js
module.exports = {
  css: {
    loaderOptions: {
      // 给 sass-loader 传递选项
      sass: {
        // @/ 是 src/ 的别名
        // 所以这里假设你有 `src/variables.scss` 这个文件
        data: `@import "@/variables.scss";`
      }
    }
  }
}
```

爬虫和标注系统就是利用这个方式实现scss文件全局使用

![](https://img-blog.csdnimg.cn/2019021519564354.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2Rlbmdkb25neGlh,size_16,color_FFFFFF,t_70)

4. webpack 相关

（1）简单的配置方式

调整webpack 配置的最简单的方式是修改` vue.config.js `中的 `configureWebpack `选项提供一个对象，该对象将会被 `webpack-merge` 合并入最终的 `webpack` 配置

```javascript
// vue.config.js
module.exports = {
  configureWebpack: {
    plugins: [
      new MyAwesomeWebpackPlugin()
    ]
  }
}
```

> 警告: 有些 `webpack` 选项是基于 `vue.config.js` 中的值设置的，所以不能直接修改。例如你应该修改 `vue.config.js` 中的 `outputDir` 选项而不是修改 `output.path`；你应该修改 `vue.config.js` 中的 `publicPath` 选项而不是修改 `output.publicPath`。这样做是因为 `vue.config.js` 中的值会被用在配置里的多个地方，以确保所有的部分都能正常工作在一起

```javascript
// 基于环境有条件的配置行为
// vue.config.js
module.exports = {
  configureWebpack: config => {
    if (process.env.NODE_ENV === 'production') {
      // 为生产环境修改配置...
    } else {
      // 为开发环境修改配置...
    }
  }
}
```

（2）链式操作

（高级，利用  `webpack-chain` 在 `vue.config.js` 中的 `chainWebpack `进行相应修改，配合` vue inspect `比较方便查看对应操作方式）

- 修改` Loader `选项

```javascript
// vue.config.js
module.exports = {
  chainWebpack: config => {
    // GraphQL Loader
    config.module
      .rule('graphql')
      .test(/\.graphql$/)
      .use('graphql-tag/loader')
        .loader('graphql-tag/loader')
        .end()
  }
}
```

- 添加新的 `Loader`
- 替换一个规则里面的 `Loader`
- 修改插件选项

补充：
[webpack-chain 的 API](https://github.com/mozilla-neutrino/webpack-chain#getting-started)

webpack-chain[阅读一些源码](https://github.com/vuejs/vue-cli/tree/dev/packages/%40vue/cli-service/lib/config)
- 审查项目的webpack 配置（该命令会将解析出来的 webpack 配置、包括链式访问规则和插件的提示打印到 stdout。）

```javascript
// 将其输出重定向到一个文件(被格式化，非有效的webpack 配置文件)以便进行查阅
vue inspect > output.js

// 只审查第一条规则
vue inspect module.rules.0

// 指向一个规则或插件的名字
vue inspect --rule vue
vue inspect --plugin html

// 列出所有规则和插件的名字
vue inspect --rules
vue inspect --plugins
```

- 以一个文件的方式使用解析好的配置

5. 环境变量和模式

（1）.env
> .env 文件知识点补充：

该文件中定义的变量，构建过程中，会被注入到` process.env `中 ，成为该对象的一个属性；但是针对于浏览器这里没有` node `环境情况下，在 `process.env`中的字段会被当做是一个全局变量，因此，在所有的页面中都可以获取到该字段的值

```javascript
// 可以替换项目根目录中的下列文件来指定环境变量
// 被载入的变量将会对 vue-cli-service 的所有命令、插件和依赖可用
.env                # 在所有的环境中被载入
.env.local          # 在所有的环境中被载入，但会被 git 忽略
.env.[mode]         # 只在指定的模式中被载入
.env.[mode].local   # 只在指定的模式中被载入，但会被 git 忽略
```


 补充：

- 优先级：特定模式环境文件(如：`.env.production`) > 一般环境文件（如：`.env`）
- 优先级：`Vue CLI` 启动时已经存在的环境变量 >  `.env` 文件。
如果在环境中有默认的 `NODE_ENV`，你应该移除它或在运行 `vue-cli-service` 命令的时候明确地设置 `NODE_ENV`。

（2）模式

- development
- production
- test

（3）在客户端侧代码中使用环境变量（在代码中始终可用的三种变量）

- 只有以 `VUE_APP_ `开头的变量会被` webpack.DefinePlugin` 静态嵌入到客户端侧的包中
- `NODE_ENV` - 会是 `"development"`、`"production"` 或 `"test"` 中的一个。具体的值取决于应用运行的模式。
- `BASE_URL` - 会和 `vue.config.js` 中的 `publicPath` 选项相符，即你的应用会部署到的基础路径。

> 补充：所有解析出来的环境变量都可以在` public/index.html `中以` HTML `插值中介绍的方式使用。

（4）只有在本地有效的变量

文件名为：`.env.local`

可在此基础上指定模式： `.env.development.local`

6.构建目标

（1）应用

（2）库

（3）Web Component 组件

7. 部署

其他总结：

[vue.config.js基本配置](http://note.youdao.com/noteshare?id=44b35da84f7d6b95153d6f7fcebbd264&sub=F186F25C20024E70962F3543C5D36B60)

[vue-cli 插件](http://note.youdao.com/noteshare?id=248eb7265f22f5524e58d1245e57008d&sub=CCF6EC6EFCD8415FBDAA162698E3A853)
