---
title: 'SSH 快速登录'
date: 2020-02-03 15:33:02
tags: [SSH]
published: true
hideInList: false
feature: 
isTop: false
---
## 省略IP、用户名、端口
编辑`~/.ssh/config`文件
```
Host alias1
    HostName 6.6.6.6
    User root
    Port 22

Host alias2
    HostName 8.8.8.8
    User root
    Port 22
```
修改完成后使用`ssh alias1`，再输入密码即可登录

## 省略密码
将`~/.ssh/id_rsa.pub`配置到服务器的`~/.ssh/authorized_keys`文件中，即可直接通过`ssh alias1`登录