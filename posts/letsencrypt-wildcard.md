---
title: 'Let''s Encrypt 通配符证书申请'
date: 2019-07-18 12:37:33
tags: [Let's Encrypt,通配符,HTTPS,SSL,证书]
published: true
hideInList: false
feature: 
---
1. 获取certbot-auto
```bash
wget https://dl.eff.org/certbot-auto
```
2. 添加执行权限
```bash
chmod u+x certbot-auto
```
3. 申请证书
```bash
sudo ./certbot-auto --server https://acme-v02.api.letsencrypt.org/directory --manual --preferred-challenges dns-01 certonly  -d "*.your-domain.com"
```
将`your-domain.com`替换成你的域名
4. 添加域名TXT记录

添加一条 `_acme-challenge.your-domain.com` 的TXT记录，记录值为第三步生成的文本。
5. 申请结束

可根据`dig _acme-challenge.your-domain.com txt`查询TXT记录是否生效，证书生成成功后保存在 `/etc/letsencrypt/live/your-domain.com/` 中，有效期为三个月，重新生成证书命令 `certbot-auto renew`。