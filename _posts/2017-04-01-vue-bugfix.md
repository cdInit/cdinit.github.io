---
layout: post
title:  "vue中使用"
categories: vue
tags: vue
author: Init
---

* content
{:toc}

vue中使用filter




## 定义filter
``` sh

//将时间戳转换成指定格式
Vue.filter('time', function (value) {//value为13位的时间戳
    function add0(m) {
        return m < 10 ? '0' + m : m
    }
    var time = new Date(parseInt(value));
    var y = time.getFullYear();
    var m = time.getMonth() + 1;
    var d = time.getDate();
    var h = time.getHours();
    var f = time.getMinutes();
    var s = time.getSeconds();

    return y + '-' + add0(m) + '-' + add0(d) + ' ' + add0(h) + ':' + add0(f) +':' + add0(s)
});

```


``` sh

//将支付代码转换成文字
Vue.filter('payType', function (payType) {
    // let payWay;
    //0未知，1现金，2支付宝，3微信
    //微信 1004 支付宝 1003 现金 1001
    if(payType == 1003){
      return '支付宝支付'
    }else if(payType == 1004){
      return '微信支付'
    }else if(payType == 1001){
      return '现金支付'
    }else{
      return '未知方式';
    }
});


```

## 使用filter

在渲染数据时候使用管道。

{{item.addTime | time}}