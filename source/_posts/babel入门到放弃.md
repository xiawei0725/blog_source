---
title: babel入门到放弃
date: 2021-09-02 20:49:16
tags: babel
categories: babel
---

## babel入门基础篇

### babel介绍

babel是一个`JavaScript`编译器，注意是编译器，不是打包器。它可以帮助用户把一些当前ES的高级特性转换成能在暂时不支持的环境里面运行；并且还能针对一些环境对某些特性做补丁操作（语法转换了，但是有些东西浏览器就是不支持），比如IE浏览器；当然它还能转换一些例如`jsx`语法转换以及类型检查`flow`语法的支持。
<!-- more -->
### 安装`babel`

```
mkdir babel_demo
cd babel_demo
npm init -y
npm i @babel/core @babel/cli -D
```
### 准备一些常规的es6代码

src/index.js

```js
let a = msg => msg + `hello`
console.log(a(123));
```

### 简单实用babel/cli命令来编译代码

```
npx babel src -d lib
```
你会发现转换编译出来的代码居然原封不动的`copy`了一份,这当然不是我们想要的

### babel配置

到上面为止，`babel`还没有帮我们做任何事，那么下面就通过编写配置的方式来告诉`babel`来如何编译代码。`babel`的配置文件可以是`json`也可以是`js`，还可以是`.babelrc`,支持的方式很多，这里以最简单最常见的`.babelrc`为例。一个常规的配置文件一般如下：

```
{
  "presets": [],
  "plugins": []
}
```

从配置文件可以看到我们需要一些预设也需要一些插件。那么什么是预设什么又是插件呢？

### 插件
插件是`babel`的核心，可以说`babel`的所有工作都是由插件完成的。通常插件主要负责我们es标准高级特性代码编译的，比如有专门处理箭头函数的插件，也有专门处理类语法的插件，当然还有针对一些框架（react）、模块化、类型检查（flow或者ts）等的插件。
### 预设
那么什么又是预设呢？ 预设可以看成是一组插件或者配置项的集合。就好比你的移动电话套餐，可能你不办套餐你需要单独开通什么电话、短信、来电显示、流量等业务，那么此时你也可以订购一个套餐来省去许多不必要的麻烦。

### 插件初体验

为了验证插件，我们拿一个箭头函数插件来体验一把，之所以不能一个个语法层面的插件来讲，是因为Es的新特性那么多，想必插件也很多，没法全部说到，而且一般项目开发也用不上，具体插件列表可以参考 [插件列表](https://babeljs.io/docs/en/plugins-list)。先写一个箭头函数，仅包含这种语法：

```js
var fn = msg => msg + '!!!'

var str = fn('hello')
console.log(str);
```
然后安装一个支持箭头函数的插件`@babel/plugin-transform-arrow-functions`来帮我们编译

```bash
npm install --save-dev @babel/plugin-transform-arrow-functions
```
然后在根目录的`.babelrc`下写上插件配置：

```js
{
  "plugins": [
    "@babel/plugin-transform-arrow-functions"
  ],
  "presets": []
}
```
**注意插件那里如果你要写额外配置，需要如下这样写**

```js
{
  "plugins": [["@babel/plugin-transform-arrow-functions", { "spec": true }]]
}
```

再次执行编译，你会惊奇的发现箭头函数代码已经被转换过来了，这里为来演示剪头函数，我特意把`let`改成了`var`，那么如果要写`let`或者`const`呢，那么我们又得再安装一个插件`@babel/plugin-transform-block-scoping`.卧槽尼玛，这也忒麻烦咯吧！好吧，接下来去找客服小姐姐办个套餐吧。

### 使用预设

最有名的预设套餐有`@babel/preset-env`(包含了一系列的语法特性插件的集合)、`@babel/preset-react`,这里我们县移除上面的箭头函数配置插件。安装我们的预设

```
npm i @babel/preset-env -D
```
然后更改配置

```js
{
  "plugins": [
   
  ],
  "presets": [
    "@babel/preset-env"
  ]
}
```

这个时候再次编译，你会发现代码还是可以正常运行。通常来讲有时候我们会配合`browserlist`来指定我们构建的目标，具体关于这个工具如何使用，可以查看 [browserslist](https://github.com/browserslist/browserslist),该工具不光babel在用，大名鼎鼎的`postcss`也在用。

### polyfill垫片

什么是垫片，上面说过`babel`除了编译代码，还能帮助我们打补丁，比如Es6新推出了 `Array.prototype.findIndex`这个方法，我们从mdn可以看到ie浏览器是并不支持的，也就是说虽然你可以写这个语法，但是特殊的环境它不支持，那怎么办，难道又需要判断是ie就不使用这个方法或者自己写一个替代上去吗？显然不是的，你想想那么多新api，每个浏览器可能都会有支持上的差异，自己写得写到什么时候。实际上社区已经有人把这个事情做了，它就是大名鼎鼎的`code-js`和`regenerator runtime`(主要是给promise打补丁)。在早期，babel有一个插件叫`@babel/polyfill`包含了上面这两个补丁库,但是现在高版本已经废弃，但是还可以使用，这个垫片就说帮助我们在全局作用域原型上扩展了对应的api.

**注意用--save来安装依赖，因为这个库需要在运行我们业务代之前就执行**
```
npm install --save @babel/polyfill
```

使用`@babel/polyfill`，它有好几种用法

1、在node\webpack\rollup等环境，你可能需要在入口文件最上面就引入
```js
require("@babel/polyfill");
```
或者

```js
import "@babel/polyfill";
```
2、在webpack中也可以通过在其配置文件中加入：

```js
module.exports = {
  entry: ["@babel/polyfill", "./app/js"],
};
```

3、搭配`@babel/preset-env`的配置项使用，这样你可以不必要在入口文件处引入垫片库了。

```js
{
  "plugins": [
   
  ],
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns":"usage",
        "modules": false
      }
    ]
  ]
}
```

**设置modules为false,是因为这个配置项就是一个摆设，谁用谁知道。另外在preset-env还有一个corejs的版本配置项，在编译的时候可能会提示你安装，你照着操作就可以了**

4、在浏览器环境通过`script`引入，不推荐

实际上至此基本使用部分就已经很明了了，但是细心的你可能已经发现，使用垫片存在一个问题就是会污染全局。

### @babel/plugin-transform-runtime

为了解决上面污染全局的问题,`babel`又给我们提供了这个`@babel/plugin-transform-runtime`插件。安装

```
npm install --save-dev @babel/plugin-transform-runtime
```

```
npm install --save @babel/runtime
```

这两个神仙组合又在干什么呢？实际上这两个是在帮我们实现一个公用的补丁，但是并不是给全局变量添加。具体配置文件如下：

```js
{
  "plugins": [
    ["@babel/plugin-transform-runtime",{
      "corejs":3
    }]
  ],
  "presets": [
    [
      "@babel/preset-env",
      {
        "useBuiltIns":"usage",
        "modules": false,
        "corejs":3
      }
    ]
  ]
}
```

我的代码：src/index.js

```js
let arr = [1,2,3]
console.log(arr.findIndex);

console.log(Object.assign);
```

在没有使用`runtime`之前 转换出来的代码是：

```js
import "core-js/modules/es.array.find-index.js";
import "core-js/modules/es.object.assign.js";
var arr = [1, 2, 3];
console.log(arr.findIndex);
console.log(Object.assign);
```

很明显，这里污染了全局了。假设我们的代码是给到第三方使用，很可能就会污染别人的环境了。

当使用了`runtime`之后转换出来的代码是：

```js
import _findIndexInstanceProperty from "@babel/runtime-corejs3/core-js-stable/instance/find-index";
import _Object$assign from "@babel/runtime-corejs3/core-js-stable/object/assign";
var arr = [1, 2, 3];
console.log(_findIndexInstanceProperty(arr));
console.log(_Object$assign);
```
可以看出原来直接使用的原型方法或者静态方法，现在都使用_开头的方法替代了。

至此关于`babel`的基础使用部分就说完咯，其实看到这里，一些对`babel`日常使用有疑惑的问题都解开了。大部分公司的业务并不会涉及到关于底层源码或者插件的编写，在后面有时间将会补充完下一篇章。

## babel自定义插件预设篇

### 插件简介
### 编写插件
### 插件原理
### 自定义预设


