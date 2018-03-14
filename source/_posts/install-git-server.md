---
title: install-git-server
date: 2018-03-14 12:05:56
tags:
---


云服务器上搭建git 

安装依赖库和编译工具
>yum install curl-devel expat-devel gettext-devel openssl-devel zlib-devel
安装编译工具
>yum install gcc perl-ExtUtils-MakeMaker

***
下载git 

>cd /usr/local/src

>wget https://www.kernel.org/pub/software/scm/git/git-2.10.0.tar.gz

***

解压和编译

>tar -zvxf git-2.10.0.tar.gz

cd git-2.10.0

执行编译

>make all prefix=/usr/local/git

这里报了个错

    >GIT_VERSION = 2.10.0
        * new build flags
        CC credential-store.o
    In file included from cache.h:4:0,
                     from credential-store.c:1:
    git-compat-util.h:280:25: fatal error: openssl/ssl.h: No such file or directory
     #include <openssl/ssl.h>
                             ^
    compilation terminated.
    make: *** [credential-store.o] Error 1

看样子是 openssl-dev  没装

>yum install openssl-devel

安装openssl-dev后继续编译

继续报错

    In file included from http.c:2:0:
    http.h:6:23: fatal error: curl/curl.h: No such file or directory
     #include <curl/curl.h>

[curl](https://stackoverflow.com/questions/11471690/curl-h-no-such-file-or-directory)

yum install curl-devel

接着编译

编译完成 安装到指定目录

    fatal error: expat.h: No such file or directory

yum install expat-devel

终于编译成功，安装

make install prefix=/usr/local/git

***

配置环境变量

将git目录加入环境变量

echo 'export PATH=$PATH:/usr/local/git/bin' >> /etc/bashrc

生效环境变量

>source /etc/bashrc

查询版本

git --version

****

创建账号及密码

>useradd -m gituser

>passwd gituser

***

初始化仓库配置用户权限


我们创建 /data/repositories 目录用于存放 git 仓库

>mkdir -p /data/repositories
进入目录初始化
>cd /data/repositories/ && git init --bare test.git

配置用户权限

>chown -R gituser:gituser /data/repositories


>chmod 755 /data/repositories

查找 git-shell 所在目录, 编辑 /etc/passwd 文件，将最后一行关于 gituser 的登录 shell 配置改为 git-shell 的目录
如下：

>which git-shell

>gituser:x:500:500::/home/gituser:/usr/local/git/bin/git-shell

***


将仓库复制到本地

>cd ~ && git clone gituser@<您的 CVM IP 地址>:/data/repositories/test.git
