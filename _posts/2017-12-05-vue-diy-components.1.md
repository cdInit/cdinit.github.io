---
layout: post
title:  "动态渲染自定义组件"
categories: vue
tags: vue
author: Init
---

* content
{:toc}

动态渲染自定义组件，主要是用到了v-for中的:is ...



## for 循环

### 主要是用到了v-for中的:is

``` sh
<!-- 渲染组件 -->
<div :is="item.component" :item="item" v-for="item in allComponents"></div>

// 其中 
// :is 为组件名称
// :item 为props的内容

import Radio from '../../components/Radio/Radio.vue'

import TextInput from '../../components/TextInput/TextInput.vue'
data(){
    return {
        allComponents:[
            {
                component:"Radio",
                componentsValue:"",
                content:'xxxx',
                field_id:"7048",
                must:"1",
                name:"单选名称"
            },
            {
                component:"TextInput",
                componentsValue:"",
                content:'xxxx',
                field_id:"1101",
                must:"1",
                name:"文本输入框"
            }
        ]
    }
},
components:{
    Radio,
    TextInput
}

通过v-for循环的:is可以动态渲染自定义组件。

```

