---
layout: post
title:  "stopPropagation, preventDefault 和 return false"
categories: javascript
tags:  javascript event
author: Init
---

* content
{:toc}

事件冒泡：子节点的事件会冒泡到父节点。比如有这么一种结构：div1为父节点，div2为子节点。div1的点击事件为alert(1)，div2的点击事件为alert(2)，
当点击div2的时候，会弹出2和1，这就是事件冒泡。





``` sh
<div id="div1">
  <div id="div2"></div>
</div>

<script>
var oDiv1 = document.getElementById("div1");
var oDiv2 = document.getElementById("div2");
oDiv2.onclick = function(){
  alert(2);
}
oDiv1.onclick = function(){
  alert(1);
}
</script>
```

浏览器默认事件：比如a链接的跳转

``` sh
<a href="a.html" id="a">怎么阻止a链接的跳转呢？</a>
<script>
var oA = document.getElementById("a");
oA.onlick = function(){
  alert('a');
}
</script>
```

当我们点击了a的时候，会弹出来a，同时会跳转到a.html

我们怎么才能阻止以上的事件冒泡和浏览器默认事件呢？

## stopPropagation
阻止冒泡：当前要阻止冒泡的事件函数中调用 event.stopPropagation()或event.cancelBubble = true(兼容ie8);

封装一个方法：

``` sh
<script>
function stopBubble(ev){
    if(ev&&ev.stopPropagation){
        ev.stopPropagation();
    }else{
        window.event.cancelBubble=true;
    }
}
</script>
```

这样就能阻止事件冒泡了。

``` sh
<div id="div1">
  <div id="div2"></div>
</div>

<script>
var oDiv1 = document.getElementById("div1");
var oDiv2 = document.getElementById("div2");
oDiv2.onclick = function(ev){
  alert(2);
  stopBubble(ev); //阻止事件冒泡
}
oDiv1.onclick = function(){
  alert(1);
}
</script>
```

当我们点击div2的时候，div1的事件就不会执行了。

## preventDefault()
阻止浏览器默认行为

调用该方法，会阻止浏览器的默认行为，比如说a标签的跳转

``` sh
<a href="a.html" id="a">怎么阻止a链接的跳转呢？</a>
<script>
var oA = document.getElementById("a");
oA.onlick = function(ev){
  var ev = ev || event;
  ev.preventDefault();
  alert('a');
}
</script>
```

这样点击a标签后，会弹出a，但是不会跳转到a.html

## return false
有人认为 return false = stopPropagation() + preventDefault(),其实是错的。return false 不但阻止事件,还可以返回对象,跳出循环等... 方便地一刀切而不够灵活,滥用易出错。

经博主测试，在原生js中，return false只能阻止浏览器的默认行为，并不能阻止事件冒泡。在使用jquery的click(function(){return false})后，return false才是 == stopPropagation() + preventDefault()；
