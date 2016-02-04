title: build the vue.js develop environment with webpack
date: 2015-10-30 21:48:28
tags: vue webpack vue-strap bootstarp
---

## 内容介绍
最近几天项目上使用了vue.js作为一个主要的开发框架，并且为了发布的方便搭配了webpack一起使用。CSS框架使用的是vue-strap(vue 对bootstrap控件做了封装)
这篇文章主要总结一下具体搭建的过程，和途中遇到的一些问题的解决办法
(一次估计写不完，下次补充)

## 主要用到的工具
- [Vue](http://cn.vuejs.org/guide/)
- [webpack](https://webpack.github.io/)
- [vue-strap](http://yuche.github.io/vue-strap/)
- [vue-router](http://vuejs.github.io/vue-router/zh-cn/index.html)

<!-- more -->

## 搭建步骤
### 1. 首先建立项目的目录结构

- demo_project/
  - dist/   `webpack生成的文件`
  - src/   `源代码文件`
     - img/   `图片文件`
     - css/   `css文件`
     - js/   `js文件`
     - components/   `定义的vue组件`
     - views/   `视图文件，其实也是vue组件`
  - webpack.config.js `webpack配置文件`

### 2. 安装vue 和webpack
```
# 进入到 demo_project 目录下
  npm install vue --save
  npm install webpack --save
```
### 3. 添加webpack配置文件
```javasript
module.exports = {
    entry: './src/index.js',
    output: {
        path: __dirname + '/dist',
        filename: 'bundle.js'
    },
    module: {
        loaders: [{
            test: /\.css$/,
            loader: 'style!css'
        }]
    }
}
```
### 4. 添加package.json文件
```
# cd 到 demo_project 目录下
npm init #跟着步骤一步一步的完成
```
最后生成的`package.json`文件如下
```js
{
  "name": "vuedemo",
  "version": "1.0.0",
  "description": "a vue demo with webpack",
  "main": "webpack.config.js",
  "dependencies": {
    "vue": "^1.0.4",
    "webpack": "^1.12.2"
  },
  "devDependencies": {},
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start":"webpack --color  --progress" //注意，这是我生成后加上的。用于执行webpack.如果webpack 安装在全局可以直接行动webpack进行打包
  },
  "keywords": [
    "vue",
    "webpack"
  ],
  "author": "frank",
  "license": "ISC"
}
```
### 5. 新增html和js入口文件
```html
<!-- src/index.html -->
<html>
<head>
    <meta charset="utf-8">
</head>

<body>
    <script type="text/javascript" src="../dist/bundle.js"></script>
</body>
```
```js
/* src/index.js  */
document.write('hello. webpack runs ok!')
```
### 6. 验证webpack是否成功
```
npm start
```
在浏览器中打开index.html .看到`hello. webpack runs ok!` 表示webpack成功了运行了

> 后面就是使用vue的loader加载的vue文件了.下次再写
> 接着来安装 vue-loader

### 7.安装vue-loader
- 安装
```
npm install vue-loader --save
```
- 修改配置文件
```
//webpack.config.js
loaders[
        ...
    {
            test:/\.vue$/,
            loader:'vue'
    }
        ...
```
- 在views目录下创建测试的vue文件
```html
<!-- hello.vue -->
<template>
    <strong>{{vueMsg}}</strong>
</template>
<script>
module.exports = {
    data() {
      return {vueMsg:'Vue hello world'}
    }
}
</script>
<style scoped>
  strong{
    background-color: #f00;
  }
</style>
```
- 在index.js文件中引入vue
```
//index.js
var Vue = require('vue')
var helloVue = require('./views/hello.vue')
 new Vue({
    el: "body",
    components: {
        hello: helloVue
    }
})
```
- 修改index.html加入body中加入demo的vue节点
```html
...
<body>
  <hello></hello>
  ...
</body>
...
```
### 8.测试vue
```
//根目录执行
npm start
```
打开index.html，看到红色背景的Vue hello world
