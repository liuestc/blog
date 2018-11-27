---
title: vps上搭建hexo
date: 2017-10-09 11:52:17
tags: diary
categories: "教程"
---

前言：由于vps到期了，看了看`vultr`比较便宜，就随手买了一个，关键是可以支持支付宝，付钱很快，冲了10$好像可以用个大半年，也挺便宜。

下面记录下在vps上搭建hexo的步骤

基本设置

vps: `vultr`
os: `Center OS7`

### 安装`ngnix`
[具体操作](http://blog.csdn.net/u012486840/article/details/52610320)
>sudo rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm

报了个错

`No package ngnix available.`

问题原因：nginx位于第三方的yum源里面，而不在centos官方yum源里面
解决方法：安装epel(Extra Packages for Enterprise Linux)

具体命令

    wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
    rpm -ivh epel-release-latest-7.noarch.rpm
    yum repolist      ##检查是否已添加至源列表

然后又安装启动nginx，发现还是不行，访问ip不能出现`welcome nginx`.
why?原因是防火墙
执行下面命令

    firewall-cmd --permanent --zone=public --add-port=80/tcp  //http协议基于TCP传输协议，放行80端口
    firewall-cmd --list-all  //查看防火墙规则
    firewall-cmd --query-service nginx //查看服务启动的状态

[nginx安装后不能访问](http://xingdong365.com/network/321.html)

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
