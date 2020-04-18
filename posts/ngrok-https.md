---
title: 'Ngrok + Let''s Encrypt 实现内网穿透'
date: 2019-09-24 09:07:59
tags: [ngrok,Let's Encrypt,HTTPS,SSL,证书]
published: true
hideInList: false
feature: 
---
## 所需材料
* 一台有公网IP的服务器
* 域名
* 放行80、443、4443端口

## 测试环境
Debian 9.9

## 步骤

### 1.更新
`apt-get update && apt-get upgrade`

### 2.安装git和go
`apt-get install build-essential git golang`

### 3.下载源码
`git clone https://github.com/inconshreveable/ngrok --depth=1`

### 4.配置域名 & 生成通配符证书

**解析`ngrok.example.com`和`*.ngrok.example.com` A记录到服务器IP**

提供两种生成方式，任选其一，将`example.com`修改为自己的域名即可。

#### 一、命令行生成（推荐）
```bash
wget https://dl.eff.org/certbot-auto
chmod u+x certbot-auto
sudo ./certbot-auto --server https://acme-v02.api.letsencrypt.org/directory --manual --preferred-challenges dns-01 certonly  -d "*.ngrok.example.com,ngrok.example.com"
```
按要求添加TXT记录，证书生成后保存在`/etc/letsencrypt/live/ngrok.example.com`

#### 二、freessl.cn 生成
生成成功后会提供`CA证书`、`证书`、`私钥`文件，分别对应的文件名为`chain.pem`、`cert.pem`、`privkey.pem`

### 5.配置证书
在`ngrok`目录下
1. 将`chain.pem`复制到`assets/client/tls/ngrokroot.crt`
2. 将`cert.pem`复制到`ngrok`目录下
3. 将`privekey.pem`复制到`ngrok`目录下

```bash
cp /etc/letsencrypt/live/ngrok.example.com/chain.pem assets/client/tls/ngrokroot.crt
cp /etc/letsencrypt/live/ngrok.example.com/cert.pem ./
cp /etc/letsencrypt/live/ngrok.example.com/privkey.pem ./
```

### 6.编译服务端
```bash
make release-server
````
编译成功后在`bin`目录下的`ngrokd`即为Ngrok服务端程序

### 7.编译客户端

#### 一、Mac
```bash
GOOS=darwin GOARCH=amd64 make release-client
```
#### 二、Windows
```bash
GOOS=windows GOARCH=amd64 make release-client
```
#### 三、Linux
```bash
GOOS=linux GOARCH=amd64 make release-client
```

### 8.启动服务端
```bash
./bin/ngrokd -tlsKey=privkey.pem -tlsCrt=cert.pem -domain="ngrok.example.com"
```
后台运行在命令后加一个`&`即可

### 9.设置客户端

#### 一、创建配置文件
```yaml
server_addr: "ngrok.example.com:4443"  
trust_host_root_certs: true  
```
保存为`ngrok.yml`
#### 二、启动服务
`80`可修改
##### Ⅰ.Mac/Linux
```bash
./ngrok -config=ngrok.yml 80
```
##### Ⅱ.Windows
创建一个`start.bat`文件
```bat
@echo on
cd %cd%
ngrok -config=ngrok.yml 80
```

### 10.补充

#### 一、固定域名
`ngrok -config=ngrok.yml -subdomain prefix 80`
