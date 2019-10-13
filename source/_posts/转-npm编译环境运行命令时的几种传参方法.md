---
title: (转)npm编译环境运行命令时的几种传参方法
date: 2019-02-22 21:39:22
tags:
---
## (转)编译环境中的几种传参方法

> 原文：[编译环境中的几种传参方法](https://crossjae.github.io/2018/01/14/npmpackage/)

### 在package中获取参数

#### 1. package.json中通过$1获取参数

```javascript
"script": {  "dev": "echo $1" // ${1} 也可以}
```

#### 2. cli中运行
```javascript
npm run dev -- hello
```
#### 3. cli输出结果
```javascript
hello
```

### 修改process.argv

#### 1. package.json中配置

```javascript
"script": {  "dev": "webpack"}
```
#### 2. webpack中获取参数

```javascript
console.log('自定义参数：', process.argv);
```

#### 3. cli中运行

```javascript
npm run dev -- hello
```

#### 4. cli输出结果

```javascript
自定义参数：PROJECTDIR:  [ '/usr/local/bin/node',  '/Users/xxx/Desktop/git/webpack-practise/node_modules/.bin/webpack',  ‘hello' ]
```

### 修改process.env方法1，package.json配置如上
#### 1. webpack中获取参数

```javascript
console.log('自定义参数：', process.env.test);
```

#### 2. cli中运行

```javascript
test=hello npm run dev
```

#### 3. cli输出结果

```javascript
自定义参数：hello
```

### 修改process.env方法2，package.json配置如上（目前采用这个方法）

#### 1. webpack中获取参数

```javascript
console.log('自定义参数：', process.env.npm_config_test);
```

#### 2. cli中运行

```javascript
npm run dev --test=hello
```

#### 3. cli输出结果

```javascript
自定义参数：hello
```