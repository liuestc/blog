---
title: centerOS6/7 上搭建Discuz
date: 2019-05-15 14:41:19
tags:
---

准备 LAMP 环境
LAMP 是 Linux、Apache、MySQL 和 PHP 的缩写，是 Discuz 论坛系统依赖的基础运行环境。我们先来准备 LAMP 环境

安装 MySQL
使用 yum 安装 MySQL：
yum install mysql-server -y
安装完成后，启动 MySQL 服务：
service mysqld restart
此实验使用 mysql 默认账户名和密码，您也可以设置自己的 MySQL 账户名和密码：
，参考下面的内容：
/usr/bin/mysqladmin -u root password 'V9vYqae7'
将 MySQL 设置为开机自动启动：
chkconfig mysqld on

***


安装 Apache 组件
使用 yum 安装 Apache 组件：
yum install httpd -y
安装之后，启动 httpd 进程：
service httpd start
把 httpd 也设置成开机自动启动：
chkconfig httpd 


***

安装 PHP
使用 yum 安装 PHP：
yum install php php-fpm php-mysql -y
安装之后，启动 PHP-FPM 进程：
service php-fpm start
启动之后，可以使用下面的命令查看 PHP-FPM 进程监听哪个端口 
netstat -nlpt | grep php-fpm
把 PHP-FPM 也设置成开机自动启动：
chkconfig php-fpm 


***

安装 Discuz
CentOS 6 没有Discuz 的 yum 源，所以我们需要下载一个Discuz 压缩包：
wget http://download.comsenz.com/DiscuzX/3.2/Discuz_X3.2_SC_UTF8.zip
下载完成后，解压这个压缩包
unzip Discuz_X3.2_SC_UTF8.zip
解压完后，就能在 upload 文件夹里看到discuz的源码了
配置 Discuz
由于PHP默认访问 /var/www/html/ 文件夹，所以我们需要把upload文件夹里的文件都复制到 /var/www/html/ 文件夹
cp -r upload/* /var/www/html/
给 /var/www/html 目录及其子目录赋予权限
chmod -R 777 /var/www/html
重启 Apache
service httpd restart

***

以上是centOS6的配置

centOS7,有一些差异，centOS7中数据库是MariaDB，两者有一定差异，执行时会出现**No package mysql-server available**，具体见[解决方法](https://www.cnblogs.com/starof/p/4680083.html)


***
然后其他有问题的点集中在Ngnix配置上，完整配置如下

```
server {
    listen       8068;
    server_name  localhost;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;
    root   /var/www/html;
    location / {
        index  index.php;
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
    location ~ \.php$ {
        root   /var/www/html;
        fastcgi_pass   127.0.0.1:9000;
        fastcgi_index  index.php;
        fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;

        fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
        fastcgi_param  PATH_INFO  $fastcgi_path_info;
        fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;


        include        fastcgi_params;
    }

    # deny access to .htaccess files, if Apache's document root
    # concurs with nginx's one
    #
    #location ~ /\.ht {
    #    deny  all;
    #}
}

```


最后背个命令：

查看进程

`ps aux | grep httpd`

杀进程

`kill -9 pid`