---
title: Hexo 多台机器共用问题
date: 2019-05-08 19:20:39
comments: true
toc: true
categories:
	- 环境搭建
---

* 搭建好的 Hexo + Github Pages是只有一个 master 并且只推送对外访问目录，不支持多台电脑使用的。这里整理了一种方案来进行多台机器共用。

 <!--more-->

## 在多点电脑上使用
### 新增一个 Hexo 分支

在 master 分支中，迁出 Hexo 分支

```
git checkout -b hexo
```

### 修改新分支中 .gitignore (设置 push 时忽略目录)文件

我的如下

```
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

### 部署

确保站点目录下的 _config.yml 中指向的代码仓库的分支为 master 分支。

没问题的话即可在该目录下进行 hexo g、hexo s、hexo g -d 等操作。

### 推送

```
git add .
git commit -m "website"
git push origin hexo
```

以后只需要在多台电脑上迁出 Hexo 分支即可。

## 存放到服务器上

在多台电脑上共用时，每次需要下载 node.js，hexo 以及 clone 项目等，如果网速不好的话，很让人抓狂（昨晚深有体会）。刚好有一台阿里的轻量级应用服务器，这样的话，将代码 clone 到 centos7 上，环境配置一次就可以了。

不过在放到服务器上时，有遇到一些问题，这里贴一下处理过程。

### 下载 nodejs

```
cd /usr/local
yum install -y gcc make gcc-c++ openssl-devel wget
wget https://nodejs.org/dist/v9.3.0/node-v9.3.0.tar.gz
tar -xf node-v9.3.0.tar.gz
cd node-v9.3.0
./configure
make && make install
```

如果 node.js 版本较低，点这里:

[如何升级nodejs](https://jingyan.baidu.com/article/574c52197e42b96c8d9dc115.html);
### 防火墙开放调试端口
#### 防火墙命令

查看防火墙状态

```
firewall-cmd --state
```

开启防火墙

```
systemctl start firewalld.service
```

在管理控制台》安全》防火墙 中设置 4000 端口，或命令行开放 4000 端口

```
firewall-cmd --zone=public --add-port=4000/tcp --permanent
```

重启防火墙

```
systemctl restart firewalld.service
```

查看开放的端口

```
netstat -ntlp
```

### 开始调试
在使用 hexo g、hexo s 之后，通过 ip + 4000 即可访问