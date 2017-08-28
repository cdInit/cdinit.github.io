---
layout: post
title:  "windows中创建github的ssh密钥"
categories: github
tags: github
author: Init
---

* content
{:toc}

windows中创建github的ssh密钥





## windows中创建github的ssh密钥

> 1. 安装git，从程序目录打开 "Git Bash"
> 2. 键入命令：ssh-keygen -t rsa -C "email@email.com"，"email@email.com"是github账号
> 3. 提醒你输入key的名称，输入如id_rsa
> 4. 在C:\Users\admin\.ssh下产生两个文件：id_rsa和id_rsa.pub
> 5. 用记事本打开id_rsa.pub文件，复制内容，在github.com的网站上到ssh密钥管理页面，添加新公钥，随便取个名字，内容粘贴刚才复制的内容。
> 6. 收工

>需要注意步骤2中产生的密钥文件在当前用户的根目录，必须把这两个文件放到当前用户目录的“.ssh”目录下才能生效。
在windows中只能在命令行下输入创建"."开头的文件夹。命令为 mkdir .ssh
