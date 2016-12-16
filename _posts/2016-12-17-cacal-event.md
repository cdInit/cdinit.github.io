---
layout: post
title:  "js知识点-事件取消"
categories: javascript
tags: javascript
author: Init
---

* content
{:toc}

事件取消的两种形式：

1.取消普通事件

2.取消绑定事件





## 取消普通事件

``` sh

function fn1() {
  alert(1)
}

function fn2(){
  alert(2)
}

document.onclick = fn1;
document.onclick = null;//普通事件取消

```

### 取消绑定事件

``` sh

ie:obj.dettachEvent(事件名称,事件函数)
document.attachEvent('onclick',fn1);
document.attachEvent('onclick',fn2);
document.detachEvent('onclick',fn1);//取消掉了document的onlick中的fn1这个事件，但是fn2仍然存在

标准:obj.removeEventListener(事件名称,事件函数,是否捕获)
document.addEventListener('click',fn1,false);
document.addEventListener('click',fn2,false);
document.removeEventListener('click',fn1,false);//取消绑定事件，仍然只取消这一个绑定事件，其他的仍然存在

```
