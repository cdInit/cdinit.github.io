---
layout: post
title:  "vue开发中在IOS遇到的坑"
categories: vue
tags: vue
author: Init
---

* content
{:toc}

> 滑动顶层，底层跟着滑动
> 特定样式下，VUE数据更新了，视图没更新





## 开发中在IOS遇到的坑

在开发的时候，经常使用 overflow-y:scroll,-webkit-overflow-scrolling: touch 来解决IOS滑动不流畅的问题，但是有时候在布局的时候又考虑不到很多种情况，以至于在某些特殊的情况下会有些怪异现象。比如：

坑1：父级有-webkit-overflow-scrolling: touch这个属性，然后通过position:absolute定位并且指定bottom和top（顶部和底部的菜单）。在子级中设置的蒙层（fixed,height:100%）在IOS中始终不能撑开到全屏，测试了半天才发现是-webkit-overflow-scrolling: touch的锅。。。所以在显示蒙层的时候把那个属性去掉就正常了。

坑2：父级有overflow-y:scroll，-webkit-overflow-scrolling: touch，在子级（弹出层）有上下滑动的东西，底部的父级也会上下滑动（诡异）。。解决：在弹出层的时候将父级设为overflow-y:hidden。

坑3：在样式中，同时存在postion:relative和display:inline（自身为inline的也不行比如span），VUE在IOS10.3.3中数据更新了但是视图没有更新。去掉其中一个属性就好了，或者将display:inline改为display:inline-block
