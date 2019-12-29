---
title: Linux 系统管理 firewalld 命令
date: 2019-05-17 12:57:59
tags:
   - Linux
   - firewalld
categories:
   - Linux
toc: true
declare: true
---

> 摘要: Linux 系统 firewalld 防火墙

![](https://raw.githubusercontent.com/aikaiqiang/aikq-blog-comments/master/notepic/christopher-gower-291246-unsplash.jpg)

<!-- more -->
# Linux 系统管理 firewalld 命令;
基于系统：CentOS7

## systemctl 管理 firewalld 服务

```bash
# 启用
systemctl start firewalld
     
# 查看状态
systemctl status firewalld
     
# 停止
systemctl disable firewalld
     
# 禁用
systemctl stop firewalld
     
```

### systemctl 管理
systemctl 是CentOS7的服务管理工具，融合之前的service和chkconfig的功能于一体
```bash
# 启动一个服务：
systemctl start firewalld.service
# 关闭一个服务：
systemctl stop firewalld.service
# 重启一个服务：
systemctl restart firewalld.service
# 显示一个服务的状态：
systemctl status firewalld.service
# 在开机时启用一个服务：
systemctl enable firewalld.service
# 在开机时禁用一个服务：
systemctl disable firewalld.service
# 查看服务是否开机启动：s
ystemctlis-enabled firewalld.service
# 查看已启动的服务列表：
systemctllist-unit-files | grep enabled
# 查看启动失败的服务列表：
systemctl--failed
```

## 配置firewalld-cmd   
```bash
# 查看版本
firewall-cmd --version

# 查看帮助 
firewall-cmd --help

# 显示状态： 
firewall-cmd --state

# 查看所有打开的端口： 
firewall-cmd --zone=public --list-ports

# 更新防火墙规则： 
firewall-cmd --reload

# 查看区域信息:  
firewall-cmd--get-active-zones

# 查看指定接口所属区域： 
firewall-cmd--get-zone-of-interface=eth0

# 拒绝所有包：
firewall-cmd --panic-on

# 取消拒绝状态： 
firewall-cmd --panic-off

#查看是否拒绝： 
firewall-cmd --query-panic
```

## 常用命令
```bash
# a.添加一个端口 （--permanent永久生效，没有此参数重启后失效）
firewall-cmd --zone=public --add-port=80/tcp --permanent

# b.重新载入
firewall-cmd --reload

# c.删除端口
firewall-cmd --zone=public --remove-port=6379/tcp --permanent

# 查看当前开了哪些端口(其实一个服务对应一个端口，每个服务对应/usr/lib/firewalld/services下面一个xml文件)
firewall-cmd --list-services

# 查看所有打开的端口： 
firewall-cmd --zone=public --list-ports

# 更新防火墙规则： 
firewall-cmd --reload

# 查看还有哪些服务可以打开：
firewall-cmd --get-services
```