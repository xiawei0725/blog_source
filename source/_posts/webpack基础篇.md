---
title: 01-webpack基础-常用基础配置
date: 2021-08-30 22:59:51
tags: webpack
categories: webpack
---
## webpack是什么

### 简介

`webpack`是一个模块打包器，在现在的前端领域，模块绝不仅仅是JS代码，其实在webpack看来，任何有效的前端资源类型均可以看作模块，而其要做的就是有效的组织这些模块，来形成可用的有效产物。
<!-- more -->
### 功能
- 代码转换
- 文件合并
- 开发环境搭建
- 代码优化
- 构建发布
- 等等
  
### 概念

默认情况下，`webpack`支持零配置，但是这种配置很弱智。所以它又支持自定义配置。用户可以通过编写一个配置文件规则来告诉`webpack`发动机如何处理我们的模块，那么这个配置就肯定需要按照官方提供的规则来编写。通常一个简单的配置文件如下

```js
module.exports = {
  entry:{},
  output:{},
  mode:'',
  module:{},
  plugins:[]
}
```

**入口**
可以是单入口，也可以是多入口，主要是指示 webpack 应该使用哪个模块，来作为构建其内部依赖图(dependency graph) 的开始。
**输出**
output 属性告诉 webpack 在哪里输出它所创建的 bundle，以及如何命名这些文件。
**模式**
webpack 内置的了三种打包模式，分别是`development`, `production` 或 `none`。每种模式其内部默认的运行机制不一样，打包出来的效果也不一样，当然你完全可以通过自己配置来覆盖或者修改内部默认的配置
**模块loader**
`webpack`将一切资源当成模块，那么通常就需要你在`module`这个配置项中定义一些`rules`，然后通过具体的规则告诉打包机某种类型的文件该利用哪些`loader`处理，例如
```js
module:{
  rules:[
    {
      test: /\.css$/,
      use: ['style-loader','css-loader'] // 用这些loader来处理css文件
    }
  ]
}
```
**插件**
那么什么又是插件呢？`loader` 用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。包括：打包优化，资源管理，注入环境变量。例如你可能需要在本地跑一个服务器或者做一些目录清理的事情，显然这些事情根模块是没多大关系，没法通过`loader`来做，因此引入了插件的概念

## 快速入门

### 初始化项目

```bash
mkdir webpack_demo && cd webpack_demo
npm init -y
```
### 安装基本依赖

```bash
npm install webpack webpack-cli -D
```

### 打包第一个应用
- 创建 `src/index.js`
```bash
mkdir src && touch src/index.js
```
- index.js
```js
console.log(`hello webpack`);
```
- 执行首次打包构建
```bash
npx webpack
```
最后你会发现生成了一个`dist`目录，虽然控制台有警告，但是并不影响最终结果。最后你可以在项目根目录创建一个`index.html`来引入这个js在浏览器运行,至此一个快速入门就结束咯。

## 加载css
## 加载图片
## 加载字体文件
## 分离css
## 使用css预处理
## 自动清理和生成html模版




