---
title: 'Nginx 搭建'
date: 2019-09-30 08:32:22
tags: [nginx]
published: true
hideInList: false
feature: 
---
## 搭建环境
CentOS 7

## 步骤

### 1.下载Nginx源码
```bash
wget http://nginx.org/download/nginx-1.16.1.tar.gz
tar xzvf nginx-1.16.1.tar.gz
cd nginx-1.16.1
```

### 2.安装所需依赖
```bash
yum install -y gcc
yum install -y pcre pcre-devel
yum install -y openssl openssl-devel
```

### 3.编译
```bash
./configure --with-http_ssl_module --with-http_v2_module
make
make install
```

### 4.创建软连接
```bash
ln -s /usr/local/nginx/sbin/nginx /usr/sbin/nginx
```







