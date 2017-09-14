---
layout: post
title:  "webpack中使用Dllplugin单独打包第三方库"
categories: webpack
tags: webpack
author: Init
---

* content
{:toc}

使用vue-cli默认配置打包项目的时候，会将用到的第三方库全部打包到vendor.js中，以至于vendor.js特别的大，LZ项目的vendor.js一度达到1.4M，打包的时候特别慢，所以想把一些比较大的库打包到单独的文件中，提高打包效率。这里我们使用 DllPlugin 和 DllReferencePlugin 达到想要的目的




## 新建dll.conf.js文件

配置参考官网：[github文档](https://github.com/webpack/docs/wiki/list-of-plugins#dllplugin)

在build文件夹中新建dll.conf.js文件，我这里是需要将echarts和xxxx单独打包成单独的文件

``` sh
var path = require('path')
var webpack = require('webpack')

module.exports = {
  //配置入口
  entry: {
    echarts: [
      'echarts'
    ],
    xxxx:[
      'xxxx'
    ]
  },
  //配置出口，这里将会把生成出来的dll文件放在/static/js中，会生成两个文件，echarts.dll.js，xxxx.dll.js
  output: {
    path: path.resolve(__dirname, '../static/js'),
    filename: '[name].dll.js',
    library: '[name]_library'
  },
  //生成 manifest.json文件，会生成两个文件到build目录中，echarts-manifest.json，xxxx-manifest.json
  plugins: [    
    new webpack.DllPlugin({
      path: path.join(__dirname, '.', '[name]-manifest.json'),     
      name: '[name]_library',
      context:__dirname
    })    
  ]
}

```

在新建完成dll.conf.js文件之后，我们就可以先把文件打包出来，命令：

``` sh
webpack --config build/dll.conf.js
```

这里我们可以添加命令到pakage.json中，避免每次都输入这么长一串命令。

不出意外的话，我们可以看见生成出来的四个文件。分别在build文件夹中的两个mainfest.json文件和../static/js中的两个js文件


## 在webpack.prod.conf.js中配置DllReferencePlugin

在plugins配置中添加：

``` sh
new webpack.DllReferencePlugin({
    context: __dirname,
    manifest: merge(require('./echarts-manifest.json'),require('./xxxx-manifest.json'))
})  
```

这里注意context上下文必须和dll.conf.js中的context上下文一致，manifest则配置刚刚生成出来的那两个json文件，因为是两个，所以这里merge一下。

## index.html中引入dll.js文件

生成并配置完毕之后，在index.html中引入生成出来的那两个js

``` sh
<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <title>管理后台</title>
</head>

<body>
  <div id="app">
  </div>
  <!-- 引入第三方库 -->
  <script src="<%= webpackConfig.output.publicPath %>static/js/echarts.dll.js"></script>
  <script src="<%= webpackConfig.output.publicPath %>static/js/xxxx.dll.js"></script>
</body>

</html>
```

## 再次打包查看效果

再次执行 npm run build 命令，我们可以看到之前vendor.js中的echarts和xxxx已经被分离出来了，vendor.js也没有那么大了。

## 思考

这个方法不能减小总的打包大小，只是在打包的时候速度更快了而已。后续首屏优化的话，还是需要懒加载等技术，下次在研究了。

