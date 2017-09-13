---
layout: post
title:  "webpack中分析打包后的文件大小以及依赖"
categories: webpack
tags: webpack
author: Init
---

* content
{:toc}

在使用vue-cli和webpack打包项目之后，总会提示vendor.js很大，但是又没有给出哪些模块导致的，我们可以使用[webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer)来分析 Webpack 生成的js组成并且可以通过网页很直观的知道，到底是哪些依赖的包导致文件过大





## 安装

> 1. 进入项目目录
> 2. npm install --save-dev webpack-bundle-analyzer

``` sh
npm install --save-dev webpack-bundle-analyzer
```

## 配置

在使用vue-cli生成的脚手架文件中，通过修改build文件夹下的webpack.prod.conf.js

> 1.引入模块 

``` sh
var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
```

>2.在plugins中加入配置

``` sh
new BundleAnalyzerPlugin()
```

## 运行

当我们再次运行 npm run build 的时候，它在打包完成之后会启动浏览器，然后显示相应的依赖和模块大小。这样就很方便后续的优化。
