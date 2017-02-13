---
title: 在github上搭建hexo博客
date: 2016-10-13 17:47:25
tags: hexo
---

**本文介绍并记录了hexo博客的搭建过程**

<!--more-->

# 版本说明
hexo: 3.2.2
node: 6.9.4

# 安装node.js

官网下载最新[node.js](https://nodejs.org/en/)并安装。

# 安装Git

官网下载最新 [Git for Windows](https://git-for-windows.github.io/) 并安装。
Git for Windows 包含git bash 和 git ui两种，其中git bash 就能满足所有需求，git ui 只是多了一个图形界面，安装的时候可以选择不安装。

# 申请 Github 账号

## 官网申请 [github](https://github.com/) 账号
## 生成 ssh key
**如果不执行这一步，那么在选择仓库地址的时候需要使用http协议，而且提交要输入github账号密码。如果配置了ssh则不需要**
## 创建一个空的仓库
建立与你用户名对应的仓库，仓库名必须为 your_user_name.github.io，比如我的仓库为 ekimin.github.io

# 安装 hexo

任意目录下右键，选择 git bash。在bash中输入：

```
npm install -g hexo
```

如果下载缓慢或失败，尝试VPN或者代理。

# 使用 hexo 创建博客
## 初始化

新建一个目录，如blog，在该目录下打开 git bash 进行以下操作：

```
hexo init 
```
初始化完成后，hexo会在当前目录生成一系列文件，这些就是博客的源码。

## 生成静态页面

```
hexo generate
或者简写
hexo g
```

完成后你会发现目录下多了一些目录和文件，这些就是 hexo 生成的静态页面相关文件。

## 本地测试博客

启动一个本地服务来测试
```
hexo server
或者
hexo s
```

在浏览器中访问 localhost:4000 查看页面

## 发布到github

- 修改博客目录下 _config.yml 文件 （注意每一项冒号后都有一个空格）
```yaml
deploy: 
  type: git #通过git部署
  repository: https://github.com/Ekimin/Ekimin.github.io.git #git仓库地址，需要提前建立 （这里用的是http协议，部署的时候会提示你输入github的账号密码，如果配置了ssh则可以直接使用ssh地址，这种方式不用输入账号密码，比如：git@github.com:Ekimin/Ekimin.github.io.git）
  branch: master #分支
```

### 部署

安装git部署插件

```
npm install hexo-deployer-git --save
```

然后 
```
hexo deploy
或者
hexo d
```

登录 ekimin.github.io 访问页面

----

