---
layout: post
title:  "js知识点-事件绑定"
categories: javascript
tags: javascript
author: Init
---

* content
{:toc}

事件绑定的两种形式：

1.直接绑定

2.通过addEventListener/attachEvent绑定，[HTML DOM addEventListener() 方法](http://www.runoob.com/jsref/met-element-addeventlistener.html)





## 直接绑定

``` sh
document.getElementById('test').onclick = function(){
  ...
}
```

这种绑定会覆盖之前的事件绑定。

## addEventListener的第三个参数

addEventListener(event,function,useCapture)

useCapture:

true - 事件句柄在捕获阶段执行。

false- false- 默认。事件句柄在冒泡阶段执行。

``` sh
document.getElementById("test").addEventListener("click", test , true);
function test(){}
```

### 事件流的执行

``` sh
<div id="div1">
  <div id="div2">
    <div id="div3"></div>
  </div>
</div>

<script>

var oDiv1 = document.getElementById('div1');
var oDiv2 = document.getElementById('div2');
var oDiv3 = document.getElementById('div3');

function test1(){alert(1)}
function test2(){alert(2)}
function test3(){alert(3)}

oDiv1.addEventListener('click',test1,true) //捕获 事件进入的时候执行
oDiv1.addEventListener('click',test2,false)//冒泡 事件出来的时候执行
oDiv3.addEventListener('click',test3,false)

//弹出顺序为 1，3，2
</script>

```

由上面的代码可以看出，当oDiv3点击的时候，在HTML结构中oDiv3为oDiv1的子节点，所以会从oDiv1的事件一层一层的往下执行，
这是事件的进入，也就是捕获。

当执行到oDiv3的点击事件的时候，会将事件返回出去，也就是冒泡。

所以上面代码的执行顺序为：点击oDiv3-->从oDiv1进入-->oDiv1添加了点击事件的捕获-->弹出1-->进入oDiv2-->oDiv2没有事件捕获-->进入oDiv3-->oDiv3没有事件捕获-->向上
反弹-->oDiv3有事件冒泡-->弹出2-->从oDiv2出-->oDiv2没有冒泡-->从oDiv1出-->oDiv1添加了冒泡事件-->弹出1

依次弹出：1，3，2
