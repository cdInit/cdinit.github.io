---
layout: post
title:  "js知识点-事件委托"
categories: javascript
tags: javascript
author: Init
---

* content
{:toc}

事件委托：利用冒泡的原理，把事件加到父级上，触发执行的效果

好处：

1.提高性能

2.新添加的元素，仍然会存在之前的事件





## 使用循环添加事件

通过循环分别为li添加事件，如果有1W个li，性能就会降低

``` sh
<ul id="ul1">
		<li>111</li>
		<li>222</li>
		<li>333</li>
		<li>444</li>
</ul>
<script type="text/javascript">
	var oUl = document.getElementById('ul1');
	var aLi = oUl.getElementsByTagName('li');
  for(var i=0;i<aLi.length;i++){
		aLi[i].onmouseover = function(){
			this.style.background = 'blue';
		}
		aLi[i].onmouseout = function(){
			this.style.background = '';
		}
	}
</script>
```

## 使用事件委托添加事件

使用事件委托的形式，注意event对象的事件源：不管在哪个事件中，只要你操作的那个元素，就是事件源

事件源兼容性：

ie: window.event.srcElement

标准：event.target

\*.target.nodeName : 事件源的nodeName

``` sh
<ul id="ul1">
		<li>111</li>
		<li>222</li>
		<li>333</li>
		<li>444</li>
</ul>
<script type="text/javascript">
	var oUl = document.getElementById('ul1');
	var aLi = oUl.getElementsByTagName('li');
	oUl.onmouseover = function(){
		var ev = ev || window.event;
		var target = ev.target || ev.srcElement;		
		if(target.nodeName.toLowerCase() == 'li'){
			target.style.background = 'blue';
		}
	}
	oUl.onmouseout = function(){
		var ev = ev || window.event;
		var target = ev.target || ev.srcElement;		
		if(target.nodeName.toLowerCase() == 'li'){
			target.style.background = '';
		}
	}
</script>
```
