---
layout: post
title:  "Vue-cli proxyTable 解决开发环境的跨域问题"
categories: javascript
tags: javascript
author: Init
---

* content
{:toc}

和后端联调时总是会面对恼人的跨域问题，最近基于Vue开发项目时也遇到了这个问题，两边各自想了一堆办法，查了一堆资料，加了一堆参数，最后还得我把自己的localhost映射成上线时将要使用的域名。
今天翻看代码时，突然发现vue-cli的config文件里有一个参数叫proxyTable，看这个名字就感觉能解决问题，于是我就去搜了一下，果然。在vuejs-templates，也就是vue-cli的使用的模板插件里，有关于API proxy的说明，使用的就是这个参数。
https://vuejs-templates.github.io/webpack/proxy.html
这个参数主要是一个地址映射表，你可以通过设置将复杂的url简化，例如我们要请求的地址是api.xxxxxxxx.com/list/1，可以按照如下设置：




``` sh

proxyTable: {
  '/list': {
    target: 'http://api.xxxxxxxx.com',
    pathRewrite: {
      '^/list': '/list'
    }
  }
}


```

这样我们在写url的时候，只用写成/list/1就可以代表api.xxxxxxxx.com/list/1.
那么又是如何解决跨域问题的呢？其实在上面的'list'的参数里有一个changeOrigin参数，接收一个布尔值，如果设置为true,那么本地会虚拟一个服务端接收你的请求并代你发送该请求，这样就不会有跨域问题了，当然这只适用于开发环境。增加的代码如下所示：

``` sh
proxyTable: {
  '/list': {
    target: 'http://api.xxxxxxxx.com',
    changeOrigin: true,
    pathRewrite: {
      '^/list': '/list'
    }
  }
}

```
vue-cli的这个设置来自于其使用的插件http-proxy-middleware
github：https://github.com/chimurai/http-proxy-middleware
深入了解的话可以看该插件配置说明，似乎还支持websocket，很强大的插件。

转自：http://www.jianshu.com/p/95b2caf7e0da
