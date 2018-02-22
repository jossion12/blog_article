---
title: "use Let's Encrypt"
date: 2018-02-20T22:19:46+08:00
tags: ["https", "Let's Encrypt"]
draft: false
---

-------



# Let's Encrypt生成个人证书

## Linux（centos 7.3）

### 安装Certbot

```bash
yum install epel-release -y
yum install certbot -y
```

### 执行certbot standalone模式生成网站证书

```bash
certbot certonly --standalone -d yourdomain.com -d www.yourdomain.com -m youmail
```

- 证书生成在/etc/letsencrypt/live/，先确保添加了“the DNS A/AAAA record(s) for that domain
     contain(s) the right IP address”

- 问题一：ImportError: No module named ‘requests.packages.urllib3’

  - 原因是系统自带的requests的版本太低，参照这个地址上的临时解决方案：

    ```
    pip install requests urllib3 pyOpenSSL --force --upgrade
    ```

- 问题二：ImportError: ‘pyOpenSSL’ module missing required functionality. Try upgrading to v0.14 or newer.

  - 原因是RHEL/CentOS的官方源和epel源的pyOpenSSL版本太旧了，新版的certbot依赖于高版本的pyOpenSSL库，从而失败。

    ```bash
    pip search certbot
    yum remove certbot pyOpenSSL
    pip install certbot
    ```

    ​

### 配置nginx(在Server配置中添加)

```bash
        server_name yourdomain.com www.yourdomain.com;
        listen 443 ssl http2;
        ssl on;
        
        ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
        ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
        ssl_dhparam /etc/ssl/private/dhparam.pem;
        ssl_session_timeout 1d;
        ssl_session_cache shared:SSL:50m;
        ssl_session_tickets on;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        
        ssl_ciphers 'ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:AES128:AES256:AES:DES-CBC3-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!RC4:!MD5:!PSK';
        ssl_prefer_server_ciphers on;
```

### 反向代理到后端服务

```bash
        location /post/* {
           proxy_pass http://httpServer:8080;
           proxy_http_version 2.0;
           proxy_set_header X_FORWARDED_PROTO https;
           proxy_set_header X-Real-IP $remote_addr;
           proxy_set_header X-Forwarded-For $remote_addr;
           proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
           proxy_set_header Host $host;
        }
```

### 证书自动更新

证书时效90天，提前30天可以更新

```bash
certbot renew --dry-run
```

使用linux contab实现各自的更新证书，需要先停止nginx

```bash
crontab -e
30 2 * */2 * stop server && /usr/bin/certbot renew --quiet && start server
```

### 证书查看

```bash
openssl x509 -noout -dates -in /etc/letsencrypt/live/yourdomain.com/cert.pem
```

### 测试服务器 SSL 安全性

又扑云 测试https的工具 : https://www.upyun.com/https