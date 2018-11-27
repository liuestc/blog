---
title: hexo部署采坑指北
date: 2018-11-26 18:09:42
tags: config&&install
categories: config&&install
---

# hexo部署采坑指北

很久没有鼓捣过博客了，以前那个vps都被清理了，上面的内容也GG了，本地找到一些备份，重新上传下，顺便记录下部署踩的一些坑。

## hexo 部署

具体参考这里[hexo部署](https://hexo.io/zh-cn/docs/deployment)
当然叙述的很简单，以前用 *rsync*部署过一次，这个比较简单，服务器直接配置就行了，这次尝试使用 *git* 方式部署。

一些值得注意的地方：

首先服务器上要有一个初始化仓库

如何初始化仓库？

`git init --bare blog.git `

*--bare* 配置项的意思是创建一个裸仓库，[具体区别](https://segmentfault.com/q/1010000004683286)

其实最主要是用了  *--bare* 后这个仓库是不能看到的，也就不能修改，一般服务器上最好这样初始化。

那看不到文件如何把他对应到响应文件夹内呢？

这里使用 *git* 的钩子函数，其实相当于监听器，每当变化时就触发。

我们重写 */blog.git/hooks/post-update*  文件，写入以下东西

```
#!/bin/sh
git --work-tree=/your_project_path --git-dir=/var/repo/blog.git checkout -f
```

上面命令相当于复制了一份相同内容到某一个目录

然后赋予其执行权限

`chmod +x post-update`

仓库和钩子就绪之后，我们尝试上传文件

执行

`hexo g -d`

试着看下服务器上有没有文件，如果有就说明上传成功了

接着配 *nginx* 这个就不表了，以前写过

主要是配置 */conf.d/default* 这个文件

配置完重启

`service nginx restart`

然后访问你的域名就有了。


****

待完成，每次还要输密码，将公钥上传到服务器可以避免这一步

免密传输很简单，两步


1. 修改本地 *~/.ssh/config* 文件，内容如下

```
Host HOST_ALIAS                       # 用于 SSH 连接的别名，最好与 HostName 保持一致
  HostName SERVER_DOMAIN              # 服务器的域名或 IP 地址
  Port SERVER_PORT                    # 服务器的端口号，默认为 22，可选
  User SERVER_USER                    # 服务器的用户名
  PreferredAuthentications publickey
  IdentityFile ~/.ssh/PRIVATE_KEY     # 本机上存放的私钥路径
```
2. 把本机公钥拷贝到服务器上的 *~./.ssh/authorized_keys* 上

然后就可以美滋滋的免密登陆了

***

相关命令熟悉

`scp -r local_project remote_path`  // 上传文件 -r  为递归上传

`locate nginx.conf`  // 查看nginx配置文件位置

`cat ~/.ssh/id_rsa.pub | clip`  // win 下为clip linux下为 pbcopy

`chomd 777`  记住r=4，w=2，x=1 读4写2执行1

`chmod +x post-update`  修改并赋予执行权限 [chmod更多内容](http://www.runoob.com/linux/linux-comm-chmod.html)

***

相关资料：

[git hooks](https://segmentfault.com/a/1190000003836345)

[服务器上配置git](https://git-scm.com/book/zh/v2/%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git-%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E6%90%AD%E5%BB%BA-Git)

[scp](http://www.runoob.com/linux/linux-comm-scp.html)

[hexo部署](https://segmentfault.com/a/1190000003836345)

