<!-- # 关于这个简洁明快的博客主题 🤘🤘🤘

随着 jekyll 的版本升级，同时我也想重构我的旧版博客主题，因此在这个月对博客进行了重构加改版，这个仓库存放我的新博客，并且我也会一直使用这个主题。目前基本改版完成，后续可能还会有些细节上的修补。

**博客访问地址：[http://gaohaoyang.github.io/](http://gaohaoyang.github.io/)**。若您喜欢这个新的博客主题，请给我个star以示鼓励吧！，欢迎大家使用。

## 目录

* [预览图](#预览图)
* [各部分详情](#各部分详情)
    * [主页 Home](#主页-home)
    * [归档页 Archives](#归档页-archives)
    * [分类页 Categories](#分类页-categories)
    * [标签页 Tags](#标签页-tags)
    * [收藏页 Collections](#收藏页-collections)
    * [展示页 Demo](#展示页-demo)
    * [关于页 About](#关于页-about)
    * [评论](#评论)
    * [目录 Contents](#目录-contents)
    * [代码高亮](#代码高亮)
    * [灯泡效果](#灯泡效果)
    * [移动端适配](#移动端适配)
    * [Footer](#footer)
    * [统计](#统计)
* [博客主题使用方法](#博客主题使用方法)
    * [1. 安装 ruby 和 jekyll 环境](#1-安装-ruby-和-jekyll-环境)
    * [2. 复制博客主题代码](#2-复制博客主题代码)
    * [3. 修改参数](#3-修改参数)
        * [基本信息](#基本信息)
        * [链接信息](#链接信息)
        * [评论信息](#评论信息)
        * [统计信息](#统计信息)
    * [4. 写文章](#4-写文章)
    * [5. 本地运行](#5-本地运行)
    * [6. 发布到 GitHub](#6-发布到-github)
* [捐助 donate](#捐助-donate)
* [Update Log](#update-log)
* [License](#license)

## 预览图

先上预览图：

主页
![index](http://ww3.sinaimg.cn/large/7011d6cfjw1f3bdli86awj211k0oyqen.jpg)

文章页
![post](http://ww4.sinaimg.cn/large/7011d6cfjw1f3bdmzb9v6j210p0j7gwn.jpg)

## 各部分详情

### 主页 Home

主页默认展示5篇文章的摘要部分，用户点击标题或阅读全文后进入文章页。右侧为近期文章、分类和标签3块区域，用户可以继续在这部分添加区域，只需修改`index.html`即可。

### 归档页 Archives

按照年份归档文章。

### 分类页 Categories

按照文章的分类，显示文章。

### 标签页 Tags

按照文章的标签显示文章。

### 收藏页 Collections

本页是用`markdown`写的，用户可以收藏自己喜欢的文章链接。

### 展示页 Demo

使用 [Masonry](http://masonry.desandro.com/) 重写了瀑布流布局，响应式布局，更好的交互体验。

### 关于页 About

对个人和对本站的介绍，使用`markdown`写的。

### 评论

支持 [多说评论](http://duoshuo.com/) 和 [disqus](https://disqus.com/) 评论。

只需要在 `_config.yml` 修改相应的配置`short_name`即可，如下：

```yml
# comments
# two ways to comment, only choose one, and use your own short name
# 两种评论插件，选一个就好了，使用自己的 short_name
duoshuo_shortname: #xxx
disqus_shortname: xxx
```

### 目录 Contents

页面滚动时目录固定在屏幕右侧，若目录高度超出屏幕高度，目录产生滚动条。

### 代码高亮

随着 jekyll 的升级，目前代码高亮使用风格与 github 上的 markdown 写法一致。

### 灯泡效果

![light](http://ww3.sinaimg.cn/large/7011d6cfjw1f3be6y4vp3j209i02rweg.jpg)

可以看到导航按钮高亮时，下面的阴影效果，我把这个称为灯泡效果。

### 移动端适配

完美适配移动端。

![mobile](http://ww4.sinaimg.cn/large/7011d6cfjw1f3bebnzxkpj20ah0fzgp4.jpg)

### Footer

**欢迎使用这个主题，使用时请保留 footer 上的模板主题来源。** Theme designed by [HyG](https://github.com/gaohaoyang).
![footer](http://ww3.sinaimg.cn/large/7011d6cfjw1f3bepd8002j20hl02ct95.jpg)

### 统计

博客支持百度统计和 Google Analytics，只需在`_config.yml`中配置响应的id即可，代码如下。

```yml
# statistic analysis 统计代码
# 百度统计 id，将统计代码替换为自己的百度统计id，即
# hm.src = "//hm.baidu.com/hm.js?xxxxxxxxxxxx";
# xxxxx字符串
baidu_tongji_id: xxxxxxxxxxxx
google_analytics_id: UA-xxxxxxxx # google 分析追踪id
```

## 博客主题使用方法

欢迎使用这个主题，以下简单说一下使用方法。

### 1. 安装 ruby 和 jekyll 环境

这一步和第5步主要是为了让博客系统在本地跑起来，如果不想在本地运行，可以无视这两步，但我还是强烈建议试着先在本地跑起来，没有什么问题后再推送的 GitHub 上。

Windows 用户可以直接使用 [RubyInstaller](http://rubyinstaller.org/) 安装 ruby 环境。后续的操作中可能还会提示安装 DevKit，根据提示操作即可。

建议使用 [RubyGems 镜像- Ruby China](https://gems.ruby-china.org/) 安装 jekyll。

安装 jekyll 命令如下

```
gem install jekyll
```

详情可以查看 jekyll 官网。[https://jekyllrb.com/](https://jekyllrb.com/) 或 中文翻译版 jekyll 官网[http://jekyllcn.com/](http://jekyllcn.com/) （中文文档翻译落后于英文官网，有兴趣有时间的小伙伴可以参与翻译，为开源世界贡献一份力哦~）

在 mac OS X El Capitan 系统下安装可能会出现问题，解决方案详情见 jekyll 官网: [ https://jekyllrb.com/docs/troubleshooting/#jekyll-amp-mac-os-x-1011]( https://jekyllrb.com/docs/troubleshooting/#jekyll-amp-mac-os-x-1011)

对 jekyll 本身感兴趣的同学可以看看 jekyll 源码: [https://github.com/jekyll/jekyll](https://github.com/jekyll/jekyll)

![jekyll logo](http://jekyllcn.com/img/logo-2x.png)

### 2. 复制博客主题代码

可以直接 clone 、下载 或 fork 这个仓库的代码即可

### 3. 修改参数

主要修改 `_config.yml` 中的参数和自己的网站小图`favicon.ico`

`_config.yml`文件中

#### 基本信息

主要用于网站头部header。

```yml
# Site settings
title: HyG
brief-intro: Front-end Dev Engineer
baseurl: "" # the subpath of your site, e.g. /blog
url: "http://gaohaoyang.github.io" # the base hostname & protocol for your site
```

#### 链接信息

主要用于网站底部footer。

```yml
# other links
twitter_username: gaohaoyang126
facebook_username: gaohaoyang.water
github_username:  Gaohaoyang
email: gaohaoyang126@126.com
weibo_username: 3115521wh
zhihu_username: gaohaoyang
linkedIn_username: gaohaoyang
dribbble_username:

description_footer: 本站记录我前端之旅的沿途风景！
```

#### 评论信息

获取`short_name`的方法：

访问 https://disqus.com/ 或 http://duoshuo.com/ 根据提示操作即可。

```yml
# comments
# two ways to comment, only choose one, and use your own short name
# 两种评论插件，选一个就好了，使用自己的 short_name
duoshuo_shortname: #hygblog
disqus_shortname: gaohaoyang
```

运行成功后，可以在 disqus 或 多说 的后台管理页看到相关信息。

#### 统计信息

获取 百度统计id 或 Google Analytics id 的方法：

访问 http://tongji.baidu.com/ 或 https://www.google.com/analytics/ 根据提示操作即可。当然，如果不想添加统计信息，这两个参数可以不填。

```yml
# statistic analysis 统计代码
# 百度统计 id，将统计代码替换为自己的百度统计id，即
# hm.src = "//hm.baidu.com/hm.js?xxxxxxxxxxxx";
# xxxxx字符串
baidu_tongji_id: cf8506e0ef223e57ff6239944e5d46a4
google_analytics_id: UA-72449510-4 # google 分析追踪id
```

成功后，进入自己的百度统计或 Google Analytics 后台管理，即可看到网站的访问量、访客等相关信息。

### 4. 写文章

`_posts`目录下存放文章信息，文章头部注明 layout(布局)、title、date、categories、tags、author(可选)，如下：

```
---
layout: post
title:  "对这个 jekyll 博客主题的改版和重构"
date:   2016-03-12 11:40:18 +0800
categories: jekyll
tags: jekyll 端口 markdown Foxit RubyGems HTML CSS
author: Haoyang Gao
---
```

下面这两行代码为产生目录时使用
```
* content
{:toc}
```

文章中存在的4次换行为摘要分割符，换行前的内容会以摘要的形式显示在主页Home上，进入文章页不影响。

换行符的设置见配置文件`_config.yml`的 excerpt，如下：

```yml
# excerpt
excerpt_separator: "\n\n\n\n"
```

使用 markdown 语法写文章。

代码风格与 GitHub 上 README 或 issue 中的一致。使用3个\`\`\`的方式。

### 5. 本地运行

本地执行

```
jekyll s
```

显示

```
Configuration file: E:/GitWorkSpace/blog/_config.yml
            Source: E:/GitWorkSpace/blog
       Destination: E:/GitWorkSpace/blog/_site
 Incremental build: disabled. Enable with --incremental
      Generating...
                    done in 6.33 seconds.
  Please add the following to your Gemfile to avoid polling for changes:
    gem 'wdm', '>= 0.1.0' if Gem.win_platform?
 Auto-regeneration: enabled for 'E:/GitWorkSpace/blog'
Configuration file: E:/GitWorkSpace/blog/_config.yml
    Server address: http://127.0.0.1:4000/
  Server running... press ctrl-c to stop.
```

在本地访问 localhost:4000 即可看到博客主页。

若安装了 Foxit 福昕pdf阅读器可能会占用4000端口，关闭 Foxit服务 或切换 jekyll 端口即可解决。详情见文章：[对这个 jekyll 博客主题的改版和重构](http://gaohaoyang.github.io/2016/03/12/jekyll-theme-version-2.0/)

若正在使用全局代理，可能会报错502，关闭全局代理即可。

### 6. 发布到 GitHub

没什么问题，推送到自己的博客仓库即可。

## 捐助 donate

您也可以捐助我喝杯咖啡！感谢！

<!-- PayPal

<form action="https://www.paypal.com/cgi-bin/webscr" method="post" target="_top">
<input type="hidden" name="cmd" value="_s-xclick">
<input type="hidden" name="hosted_button_id" value="Q44JFSYQXBFL2">
<input type="image" src="https://www.paypalobjects.com/webstatic/en_US/btn/btn_donate_cc_147x47.png" border="0" name="submit" alt="PayPal——最安全便捷的在线支付方式！">
<img alt="" border="0" src="https://www.paypalobjects.com/zh_XC/i/scr/pixel.gif" width="1" height="1">
</form><br>      -->

|                                   支付宝                                    |                                  微信支付                                   |                                                                     PayPal                                                                     |
|:---------------------------------------------------------------------------:|:---------------------------------------------------------------------------:|:----------------------------------------------------------------------------------------------------------------------------------------------:|
| ![alipay](http://ww2.sinaimg.cn/large/7011d6cfjw1f3bk8ikzoij20740743z5.jpg) | ![wechat](http://ww2.sinaimg.cn/large/7011d6cfjw1f3bkdw3bslj206z06q3z6.jpg) | [![PayPal](https://www.paypalobjects.com/webstatic/paypalme/images/pp_logo_small.png)<br>Donate via PayPal ](https://www.paypal.me/gaohaoyang) |

感谢捐赠的小伙伴！！！

* 2016.11.16 收到 微信用户 ¥6.66
* 2016.11.16 收到 微信用户 ¥1.00
* 2016.10.24 收到 奇峰 ¥6.66
* 2016.10.21 收到 旭廷 ¥10.00
* 2016.09.24 收到 鑫 ¥6.66
* 2016.08.25 收到 Erlend Aakre $2.50
* 2016.08.10 收到 微信用户 ¥4.40
* 2016.07.25 收到 邓炳初 ¥6.66
* 2016.07.11 收到 彦风 ¥6.66
* 2016.07.07 收到 Klci ¥2.50
* 2016.05.08 收到 1057 ¥10.57
* 2016.05.07 收到 吴林 ¥2
* 2016.04.29 收到 北归 ¥10
* 2016.04.28 收到 魏楚阳_Brian ¥2
* 2016.04.28 收到 薛彬 ¥8.8

## Update Log

### 2016.6.20

* `[+]` 在文章页中添加上一篇和下一篇文章链接。
* `[^]` 修改 font-family 顺序，避免微软雅黑将单引号解析为全角。
* `[^]` 修复标签云算法中被除数为零的 bug。[#26](https://github.com/Gaohaoyang/gaohaoyang.github.io/issues/26), [#28](https://github.com/Gaohaoyang/gaohaoyang.github.io/issues/28), [#30](https://github.com/Gaohaoyang/gaohaoyang.github.io/issues/30)

### 2016.5.11 v2.0.1

* `[^]` 优化代码，将页面中的大段评论相关代码抽离出来，放入`comments.html`
* `[+]` 添加百度统计和Google分析代码，在`_config.yml`中配置相关参数即可
* `[+]` 更新文档，添加博客主题使用方法，便于上手
* `[+]` 添加了`favicon.ico`
* `[^]` 修复 bug，目录太长时，滚动到最底部时隐藏到footer下面。修复后长目录在滚动到底部时使用`position:absolute`
* `[^]` 修改目录区的滚动条样式（仅针对`webkit`内核浏览器）
* `[^]` 修改 demo 页中 disqus 评论区 a 标签的颜色 bug，修改 blockqoute 中 p 标签的 margin
* `[+]` 添加不蒜子计数功能，在footer上显示访问量
* `[+]` 添加回到顶部功能

### 2016.4.27 v2.0.0

* `[^]` 基于 jekyll 3.1.2 重构了所有代码
* `[+]` 主页添加了摘要，在正文中使用4个换行符来分割，可在`_config.yml`中修改
* `[+]` 主页添加了近期文章、分类列表和标签云
* `[+]` 主页导航区做了视觉优化，阴影效果
* `[+]` 增加了归档、标签和分类页面
* `[+]` 增加了收藏页面
* `[+]` 评论插件可以选择 disqus 或 多说，直接在`_config.yml`中修改
* `[+]` 适配移动端
* `[+]` 页面滚动时，文章目录固定在右侧
* `[+]` 页面内容较少时，固定 footer 在页面底部
* `[^]` 使用 GitHub 风格的代码高亮写法，即\`\`\`的写法，去除`highlight.js`代码高亮插件的使用
* `[^]` 使用 Masonry 重写了 Demo 页中的瀑布流布局，响应式交互体验更好
* `[-]` 去除了 jQuery 和 BootStrap，使得加载速度更快

关于旧版博客，我不再维护，同时我把代码转移到了另一个仓库，见 [Gaohaoyang/old-blog](https://github.com/Gaohaoyang/old-blog)。

## License

[MIT License](https://github.com/Gaohaoyang/gaohaoyang.github.io/blob/master/LICENSE.md) -->
