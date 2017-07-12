---
layout: post
title:  "node中设置跨域请求"
categories: node
tags: node
author: Init
---

* content
{:toc}

node中设置跨域请求





## node中设置跨域请求

``` sh
app.all('*', function(req, res, next) {
    res.header("Access-Control-Allow-Origin", "*");
    res.header("Access-Control-Allow-Headers", "Content-Type,Content-Length, Authorization, Accept,X-Requested-With");
    res.header("Access-Control-Allow-Methods","PUT,POST,GET,DELETE,OPTIONS");
    if(req.method=="OPTIONS") res.send(200);/*让options请求快速返回*/
    else next();
});

```
