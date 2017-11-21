---
layout: post
title:  "SourceTree 启动不需要账号[转]"
categories: github
tags: github
author: Init
---

* content
{:toc}

> sourceTree安装之后，由于没有翻墙，始终打不开输入账号的网页。





## 新建json文件

```

[
  {
    "$id": "1",
    "$type": "SourceTree.Api.Host.Identity.Model.IdentityAccount, SourceTree.Api.Host.Identity",
    "Authenticate": true,
    "HostInstance": {
      "$id": "2",
      "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountInstance, SourceTree.Host.AtlassianAccount",
      "Host": {
        "$id": "3",
        "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountHost, SourceTree.Host.AtlassianAccount",
        "Id": "atlassian account"
      },
      "BaseUrl": "https://id.atlassian.com/"
    },
    "Credentials": {
      "$id": "4",
      "$type": "SourceTree.Model.BasicAuthCredentials, SourceTree.Api.Account",
      "Username": "",
      "Email": null
    },
    "IsDefault": false
  }
]
```

## 放到指定目录

C:\Users\Administrator\AppData\Local\Atlassian\SourceTree目录（具体情况按你的安装目录看）下手动创建 accounts.json，内容如上

源网址：http://www.cnblogs.com/qingmaple/p/6901837.html