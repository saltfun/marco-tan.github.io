---
title: '解决Ubuntu的中文乱码问题'
date: 2020-01-09 12:49:10
tags: [Ubuntu,乱码]
published: true
hideInList: false
feature: 
isTop: false
---
## 中文显示问题

首先，安装中文支持包language-pack-zh-hans：
```shell
$ sudo apt-get install language-pack-zh-hans
```
然后，修改/etc/environment（在文件的末尾追加）：
```
LANG="zh_CN.UTF-8"
LANGUAGE="zh_CN:zh:en_US:en"
```
再修改/var/lib/locales/supported.d/local(没有这个文件就新建，同样在末尾追加)：
```
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
zh_CN.GBK GBK
zh_CN GB2312
```
最后，执行命令：

```shell
$ sudo locale-gen
```
对于中文乱码是空格的情况，安装中文字体解决。
```shell
$ sudo apt-get install fonts-droid-fallback ttf-wqy-zenhei ttf-wqy-microhei fonts-arphic-ukai fonts-arphic-uming
```

## 中文输入问题
修改或者新建`.inputrc`文件
```shell
vim ~/.inputrc
```
在结尾处增加下面四行
```
set meta-flag on
set convert-meta off
set input-meta on
set output-meta on
```

加入环境变量
```shell
vim ~/.bashrc
```
在结尾处增加下面三行
```
export LANG=LANG="zh_CN.utf-8"
export LANGUAGE="zh_CN:zh:en_US:en"
export LC_ALL="zh_CN.utf-8"
```
使配置生效：
```shell
source ~/.bashrc
```
