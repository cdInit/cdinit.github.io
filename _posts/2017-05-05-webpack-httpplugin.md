---
layout: post
title:  "使用gulp实现前端自动化部署"
categories: gulp
tags: gulp
author: Init
---

* content
{:toc}

需求：使用vue-cli，npm run build的时候生成index.html文件，在index.html文件中需要按需加载script和socket的链接





## 1、配置config/index.js

``` sh
module.exports = {
  build: {
    env: require('./prod.env'), //这里依赖prod.evn.js
    index: path.resolve(__dirname, '../dist/index.html'),
    assetsRoot: path.resolve(__dirname, '../dist'),
    assetsSubDirectory: 'static',
    assetsPublicPath: '',
    productionSourceMap: true,
    // Gzip off by default as many popular static hosts such as
    // Surge or Netlify already gzip all static assets for you.
    // Before setting to `true`, make sure to:
    // npm install --save-dev compression-webpack-plugin
    productionGzip: false,
    productionGzipExtensions: ['js', 'css']
  }
```
## 2、配置prod.env.js

``` sh
module.exports = {
  NODE_ENV: '"production"',
  SOCKET_SCRIPT:'http://www.xxxxx.com:8970',
  SOCKET_CONNECT:'www.xxxxx.com:8970'
}
```

## 3、配置webpack.prod.conf.js

在HtmlWebpackPlugin插件中配置socketScript和socketConnect

``` sh
var env = config.build.env //这里依赖1中配置的文件
new HtmlWebpackPlugin({ //生成html的配置
      filename: config.build.index,
      template: 'index.html',
      socketScript:env.SOCKET_SCRIPT, //配置文件
      socketConnect:env.SOCKET_CONNECT,
      inject: true,
      minify: {
        removeComments: true,
        collapseWhitespace: true,
        removeAttributeQuotes: true
        // more options:
        // https://github.com/kangax/html-minifier#options-quick-reference
      },
      // necessary to consistently work with multiple chunks via CommonsChunkPlugin
      chunksSortMode: 'dependency'
    }),

```

## 4、在index.html中配置自定义的路径

<%= htmlWebpackPlugin.options.socketScript %> 这个是重点

``` sh
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>xxxx</title>
    <script src= '<%= htmlWebpackPlugin.options.socketScript %>/socket.io/socket.io.js' ></script>
    <script>
      var socket = io.connect('<%= htmlWebpackPlugin.options.socketConnect %>');
    </script>
    
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>

```

## npm run build

以后每次编译之后，只需要执行npm run build，上面的路径就自动配制成功了。

然后就可以添加不同的配置命令达到不同环境配置不同的url链接。