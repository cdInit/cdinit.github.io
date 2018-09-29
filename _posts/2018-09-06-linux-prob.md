---
layout: post
title:  "linux安装后的一些问题"
categories: java
tags: java
author: Init
---

* content
{:toc}

linux安装后的一些问题:

1. ssh相关
2. mysql安装
3. 防火墙相关
4. hostname相关






## ssh连接The authenticity of host can't be established

```
ssh连接The authenticity of host can't be established

修改/etc/ssh/ssh_config文件的配置，以后则不会再出现此问题

最后面添加：

StrictHostKeyChecking no

UserKnownHostsFile /dev/null

```

## 安装MYSQL

```
1. 下载mysql的repo源

$ wget http://repo.mysql.com/mysql-community-release-el7-5.noarch.rpm

2. 安装mysql-community-release-el7-5.noarch.rpm包

$ sudo rpm -ivh mysql-community-release-el7-5.noarch.rpm
安装这个包后，会获得两个mysql的yum repo源：/etc/yum.repos.d/mysql-community.repo，/etc/yum.repos.d/mysql-community-source.repo。

3. 安装mysql

$ sudo yum install mysql-server
根据步骤安装就可以了，不过安装完成后，没有密码，需要重置密码。

4. 重置密码

重置密码前，首先要登录

$ mysql -u root
登录时有可能报这样的错：ERROR 2002 (HY000): Can‘t connect to local MySQL server through socket ‘/var/lib/mysql/mysql.sock‘ (2)，原因是/var/lib/mysql的访问权限问题。下面的命令把/var/lib/mysql的拥有者改为当前用户：

$ sudo chown -R openscanner:openscanner /var/lib/mysql
然后，重启服务：

$ service mysqld restart
接下来登录重置密码：

$ mysql -u root
mysql > use mysql;mysql > update user set password=password(‘123456‘) where user=‘root‘;mysql > exit;

5. 需要更改权限才能实现远程连接MYSQL数据库

可以通过以下方式来确认：
root#mysql -h localhost -uroot -p
Enter password: ******
Welcome to the MySQL monitor.   Commands end with ; or \g.
Your MySQL connection id is 4 to server version: 4.0.20a-debug
Type ‘help;’ or ‘\h’ for help. Type ‘\c’ to clear the buffer.
mysql> use mysql; (此DB存放MySQL的各种配置信息)
Database changed
mysql> select host,user from user; (查看用户的权限情况)
mysql> select host, user, password from user;
+-----------+------+-------------------------------------------+
| host       | user | password                                   |
+-----------+------+-------------------------------------------+
| localhost | root | *4ACFE3202A5FF5CF467898FC58AAB1D615029441 |
| 127.0.0.1 | root | *4ACFE3202A5FF5CF467898FC58AAB1D615029441 |
| localhost |       |                                            |
+-----------+------+-------------------------------------------+
4 rows in set (0.01 sec)
由此可以看出，只能以localhost的主机方式访问。
解决方法：

mysql> Grant all privileges on *.* to 'root'@'%' identified by '123456' with grant option;

(%表示是所有的外部机器，如果指定某一台机，就将%改为相应的机器名；‘root’则是指要使用的用户名，)

mysql> flush privileges;    (运行此句才生效，或者重启MySQL)
Query OK, 0 rows affected (0.03 sec)

再次查看。。
mysql> select host, user, password from user;
+-----------+------+-------------------------------------------+
| host       | user | password                                   |
+-----------+------+-------------------------------------------+
| localhost | root | *4ACFE3202A5FF5CF467898FC58AAB1D615029441 |
| 127.0.0.1 | root | *4ACFE3202A5FF5CF467898FC58AAB1D615029441 |
| localhost |       |                                            |
| %          | root | *4ACFE3202A5FF5CF467898FC58AAB1D615029441 |
+-----------+------+-------------------------------------------+
4 rows in set (0.01 sec)

```

## 关闭防火墙

```

CentOS7使用firewalld打开关闭防火墙与端口
1、firewalld的基本使用
启动： systemctl start firewalld
关闭： systemctl stop firewalld
查看状态： systemctl status firewalld 
开机禁用  ： systemctl disable firewalld
开机启用  ： systemctl enable firewalld
 
 
2.systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。
启动一个服务：systemctl start firewalld.service
关闭一个服务：systemctl stop firewalld.service
重启一个服务：systemctl restart firewalld.service
显示一个服务的状态：systemctl status firewalld.service
在开机时启用一个服务：systemctl enable firewalld.service
在开机时禁用一个服务：systemctl disable firewalld.service
查看服务是否开机启动：systemctl is-enabled firewalld.service
查看已启动的服务列表：systemctl list-unit-files|grep enabled
查看启动失败的服务列表：systemctl --failed

3.配置firewalld-cmd

查看版本： firewall-cmd --version
查看帮助： firewall-cmd --help
显示状态： firewall-cmd --state
查看所有打开的端口： firewall-cmd --zone=public --list-ports
更新防火墙规则： firewall-cmd --reload
查看区域信息:  firewall-cmd --get-active-zones
查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
拒绝所有包：firewall-cmd --panic-on
取消拒绝状态： firewall-cmd --panic-off
查看是否拒绝： firewall-cmd --query-panic
 
那怎么开启一个端口呢
添加
firewall-cmd --zone=public --add-port=80/tcp --permanent    （--permanent永久生效，没有此参数重启后失效）
重新载入
firewall-cmd --reload
查看
firewall-cmd --zone= public --query-port=80/tcp
删除
firewall-cmd --zone= public --remove-port=80/tcp --permanent
```

## 修改hostname

```
centos修改主机名的正确方法
1 centos6下修改hostname

[root@centos6 ~]$ hostname   查看当前的hostnmae
s1.cdinit.com
[root@centos6 ~]$ vim /etc/sysconfig/network     编辑network文件修改hostname行（重启生效）
[root@centos6 ~]$ cat /etc/sysconfig/network     检查修改
NETWORKING=yes
HOSTNAME=centos66.magedu.com
[root@centos6 ~]$ hostname centos66.magedu.com   设置当前的hostname(立即生效，重启失效）
[root@centos6 ~]$ vim /etc/hosts    编辑hosts文件，给127.0.0.1添加hostname
[root@centos6 ~]$ cat /etc/hosts    检查
127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4 centos66.magedu.com
::1 localhost localhost.localdomain localhost6 localhost6.localdomain6

2 centos7修改hostname


[root@centos7 ~]$ hostnamectl set-hostname centos77.magedu.com             # 使用这个命令会立即生效且重启也生效
[root@centos7 ~]$ hostname    # 查看下
centos77.magedu.com
[root@centos7 ~]$ vim /etc/hosts    # 编辑下hosts文件， 给127.0.0.1添加hostname
[root@centos7 ~]$ cat /etc/hosts    # 检查
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 centos77.magedu.com
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

也可以编辑 /etc/hostname文件
```