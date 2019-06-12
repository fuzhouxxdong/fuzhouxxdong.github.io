---
title: frp https 配置
date: 2018-09-11 20:53:04
tags: 瞎搞
categories: Testing
keywords: 瞎搞,frp,https
---

各种原因吧需要调本地项目,选择了frp,开始以为https配置很简单,搞了好久没成功,网上有说的模糊，今天实验成功了，记录一下,server 端配置,测试环境centos7
#### frps配置 
```
[common]
bind_port = 7000 
vhost_http_port = 8080 # 不要改成80不然下面的用的nginx起不来
token = xxxxxx12341234
log_file = ./frps.log
```
#### 编写frps.service 
/usr/lib/systemd/system
```
[Unit]
Description=frps daemon
After=network.target

[Service]
Type=simple
ExecStart=/opt/frp_0.21.0_linux_amd64/frps -c /opt/frp_0.21.0_linux_amd64/frps.ini
Restart=always
RestartSec=1min
ExecStop=/bin/kill $MAINPID

[Install]
WantedBy=multi-user.target
```
#### frps.service启动,停止命令
```bash
systemctl enable frps
systemctl start frps
systemctl stop frps
```
<!--more-->

#### nginx配置,新建域名配置文件

```bash
[root@dev conf.d]# ls
default.conf  dev.xxx.com.conf
[root@dev conf.d]# cat dev.xxx.com.conf 
server {
    server_name  dev.xxx.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    #location / {
    #    root   /usr/share/nginx/html;
    #   index  index.html index.htm;
    #}

    location / {
        proxy_pass   http://127.0.0.1:8080;
        #这个端口是frps配置的那个,让域名转发到这个内网地址,直接http://dev.xxx.com:8080也可以访问但是不是https
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto  $scheme;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```
#### 生成ssl证书,使用[certbot](https://certbot.eff.org/lets-encrypt/centosrhel6-nginx)生成ssl证书,生成后dev.xxx.com.conf如下
```
[root@dev conf.d]# cat dev.xxx.com.conf 
server {
    server_name  dev.xxx.com;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    #location / {
    #    root   /usr/share/nginx/html;
    #   index  index.html index.htm;
    #}

    location / {
        proxy_pass   http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto  $scheme;
    }

    #error_page  404              /404.html;

    # redirect server error pages to the static page /50x.html
    #
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

    # proxy the PHP scripts to Apache listening on 127.0.0.1:80
    #
    #location ~ \.php$ {
    #    proxy_pass   http://127.0.0.1;
    #}

    # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
    #
    #location ~ \.php$ {
    #    root           html;
    #    fastcgi_pass   127.0.0.1:9000;
    #    fastcgi_index  index.php;
    #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
    #    include        fastcgi_params;
    #}

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}


    listen 443 ssl; # managed by Certbot
    ssl_certificate /etc/letsencrypt/live/dev.xxx.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/dev.xxx.com/privkey.pem; # managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot

}

server {
    if ($host = dev.xxx.com) {
        return 301 https://$host$request_uri;
    } # managed by Certbot


    server_name  dev.xxx.com;
    listen 80;
    return 404; # managed by Certbot


}

```

#### 以上全是server端配置,下面是frpc配置

```
[common]
server_addr = vps服务器ip
server_port = 7000 #frps端口
token = xxxxxx12341234
#log_file = ./frpc.log

[web]
type = http
local_port = 8080
custom_domains = dev.xxx.com #绑定了vps地址和有ssl证书的域名
```

#### 完事，现在可以访问了
![gt6md1](https://files.catbox.moe/gt6md1.png)

