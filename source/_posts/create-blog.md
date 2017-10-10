---
title: vps上搭建hexo
date: 2017-10-09 11:52:17
tags: diary
categories: "教程"
---

前言：由于vps到期了，看了看`vultr`比较便宜，就随手买了一个，关键是可以支持支付婊，付钱很快，冲了10$好像可以用个大半年，也挺便宜。

下面记录下在vps上搭建hexo的步骤

基本设置

vps: `vultr`
os: `Center OS7`

### 安装`ngnix`
#### nginx的基本使用
安装后`nginx`在`etc`文件夹下,配置文件为`/nginx/conf.d/default.conf`,具体长这样：

```bash
server {
    listen       80;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;

    location / {
        root   /usr/share/nginx/html;
        index index.html;
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
                 

可以看到默认目录在这里
```
   location / {
        root   /usr/share/nginx/html;
        index index.html;
    }
```

我们只需让这个目录和本地的hexo下的public文件夹保持一致即可
### 安装`hexo`

装完hexo后先在本地跑一跑，具体官网已经很清楚了。
关于部署有很多种方式 [具体看这里](https://hexo.io/zh-cn/docs/deployment.html)
主题当然也有很多种，挑个自己喜欢的就好

####  相关资料

[nginx相关](http://seanlook.com/2015/05/17/nginx-install-and-config/)
[hexo官网](https://hexo.io/zh-cn/docs/)
[material主题](https://material.viosey.com/docs/#/)
