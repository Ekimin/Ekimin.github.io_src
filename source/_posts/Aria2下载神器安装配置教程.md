title: Aria2安装配置
date: 2016-12-07 23:31:31
tags: aria2
categories: 常用工具
layout:
updated:
comments:
permalink:
---

# 认识 Aria2

Aria2 是一个轻量级多协议和多源 命令行 下载实用工具。它支持 HTTP / HTTPS, FTP, SFTP, bt 和 Metalink。通过内置 Aria2 可以操作 json - rpc 和 xml - rpc。对，Aria2 没有 GUI 图形界面，只有粗糙的命令行界面！但这也正是 Aria2 之轻快好省所在。

{% asset_image http://oduq3lfcc.bkt.clouddn.com/aria2UI.png AriaUI %}

----
<!--more-->
# linux：

## 下载

----
* 直接在软件仓库下载：
```shell
yum install aria2 --或
apt-get aria2
……
```

----

* 在mint上用软件包管理器下载的aria2默认安装在 /usr/share/doc下。
* 也可以下载源码安装。

## 配置
### 建立目录 
* /etc/software/aria2 用于放置配置文件的地方
* /home/aria2/downloads 用于放下载的文件

### 新建文件 
* /etc/software/aria2/aria2.conf
* /etc/software/aria2/aria2.session
* /etc/software/aria2/Aria2.log


```shell
desktop aria2 # pwd
/etc/software/aria2
desktop aria2 # ll
total 12
drwxr-xr-x 2 root root 4096 7月  23 18:12 ./
drwxr-xr-x 3 root root 4096 7月  23 18:11 ../
-rw-r--r-- 1 root root 3846 7月  23 18:12 aria2.conf
-rw-r--r-- 1 root root    0 7月  23 18:12 aria2.session
```

### 修改ari2.conf：（比较重要的是dir log input-file  save-session四个属性）
```config
## '#'开头为注释内容, 选项都有相应的注释说明, 根据需要修改 ##
## 被注释的选项填写的是默认值, 建议在需要修改时再取消注释  ##

## 文件保存相关 ##

# 文件的保存路径(可使用绝对路径或相对路径), 默认: 当前启动位置
dir=/home/aria2/downloads
# 日志文件保存路径
log=/etc/software/aria2/Aria2.log
# 启用磁盘缓存, 0为禁用缓存, 需1.16以上版本, 默认:16M
#disk-cache=32M
# 文件预分配方式, 能有效降低磁盘碎片, 默认:prealloc
# 预分配所需时间: none < falloc ? trunc < prealloc
# falloc和trunc则需要文件系统和内核支持
# NTFS建议使用falloc, EXT3/4建议trunc, MAC 下需要注释此项
file-allocation=trunc
# 断点续传
continue=true

## 下载连接相关 ##

# 最大同时下载任务数, 运行时可修改, 默认:5
max-concurrent-downloads=5
# 同一服务器连接数, 添加时可指定, 默认:1
max-connection-per-server=5
# 最小文件分片大小, 添加时可指定, 取值范围1M -1024M, 默认:20M
# 假定size=10M, 文件为20MiB 则使用两个来源下载; 文件为15MiB 则使用一个来源下载
min-split-size=10M
# 单个任务最大线程数, 添加时可指定, 默认:5
split=5
# 整体下载速度限制, 运行时可修改, 默认:0
#max-overall-download-limit=0
# 单个任务下载速度限制, 默认:0
#max-download-limit=0
# 整体上传速度限制, 运行时可修改, 默认:0
#max-overall-upload-limit=0
# 单个任务上传速度限制, 默认:0
#max-upload-limit=0
# 禁用IPv6, 默认:false
disable-ipv6=true

## 进度保存相关 ##

# 从会话文件中读取下载任务
input-file=/etc/software/aria2/aria2.session
# 在Aria2退出时保存`错误/未完成`的下载任务到会话文件
save-session=/etc/software/aria2/aria2.session
# 定时保存会话, 0为退出时才保存, 需1.16.1以上版本, 默认:0
#save-session-interval=60

## RPC相关设置 ##

# 启用RPC, 默认:false
enable-rpc=true
# 允许所有来源, 默认:false
rpc-allow-origin-all=true
# 允许非外部访问, 默认:false
rpc-listen-all=true
# 事件轮询方式, 取值:[epoll, kqueue, port, poll, select], 不同系统默认值不同
#event-poll=select
# RPC监听端口, 端口被占用时可以修改, 默认:6800
#rpc-listen-port=6800
# 设置的RPC授权令牌, v1.18.4新增功能, 取代 --rpc-user 和 --rpc-passwd 选项
#rpc-secret=<TOKEN>
# 设置的RPC访问用户名, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-user=<USER>
# 设置的RPC访问密码, 此选项新版已废弃, 建议改用 --rpc-secret 选项
#rpc-passwd=<PASSWD>

## BT/PT下载相关 ##

# 当下载的是一个种子(以.torrent结尾)时, 自动开始BT任务, 默认:true
#follow-torrent=true
# BT监听端口, 当端口被屏蔽时使用, 默认:6881-6999
listen-port=51413
# 单个种子最大连接数, 默认:55
#bt-max-peers=55
# 打开DHT功能, PT需要禁用, 默认:true
enable-dht=false
# 打开IPv6 DHT功能, PT需要禁用
#enable-dht6=false
# DHT网络监听端口, 默认:6881-6999
#dht-listen-port=6881-6999
# 本地节点查找, PT需要禁用, 默认:false
#bt-enable-lpd=false
# 种子交换, PT需要禁用, 默认:true
enable-peer-exchange=false
# 每个种子限速, 对少种的PT很有用, 默认:50K
#bt-request-peer-speed-limit=50K
# 客户端伪装, PT需要
peer-id-prefix=-TR2770-
user-agent=Transmission/2.77
# 当种子的分享率达到这个数时, 自动停止做种, 0为一直做种, 默认:1.0
seed-ratio=0
# 强制保存会话, 即使任务已经完成, 默认:false
# 较新的版本开启后会在任务完成后依然保留.aria2文件
#force-save=false
# BT校验相关, 默认:true
#bt-hash-check-seed=true
# 继续之前的BT任务时, 无需再次校验, 默认:false
bt-seed-unverified=true
# 保存磁力链接元数据为种子文件(.torrent文件), 默认:false
bt-save-metadata=true
```

### 启动服务并指定aria2配置文件的路径

```
desktop aria2 # aria2c --conf-path /etc/software/aria2/aria2.conf 

07/23 18:13:30 [WARN] Neither --rpc-secret nor a combination of --rpc-user and --rpc-passwd is set. This is insecure. It is extremely recommended to specify --rpc-secret with the adequate secrecy or now deprecated --rpc-user and --rpc-passwd.

07/23 18:13:30 [NOTICE] IPv4 RPC: listening on TCP port 6800
```

# Window 
基本上Window配置和linux差不多这里简单说下不同的地方
## 下载
**[Aria2官方下载地址](http://sourceforge.net/projects/aria2/files/stable/)**

## 配置
### 新建文件 
* aria2.conf
* aria2.session
* Aria2.log
* HideRun.vbs

同样修改配置文件conf，指定好下载目录，session文件和log文件的地址

而后修改 HideRun.vbs，将 Aria2c.exe 与配置文件 Aria2.conf 链接，并实现无命令行启动。 那么日后打开 Aria2 就双击 HideRun.vbs 这个文件而不是双击 aria2c.exe。
```
CreateObject("WScript.Shell").Run "D:\Aria2\aria2c.exe） --conf-path=aria2.conf",0
```
将上面路径修改为你aria2c.exe的路径。
到这里 Aria2 就配置好了，如果要添加开机自启动将 HideRun.vbs 的快捷方式拖入启动文件夹就 OK 了。

# 使用WebUI管理下载
为了方便管理下载，我们可以使用网页来管理下载。
* WebUI地址 http://aria2c.com/
* 若提示RPC服务配置出错：
JSON-RPC Path 默认为: http://localhost:6800/jsonrpc
host: 指运行 Aria2 所在机器的 IP 或者名字
port: 使用 --rpc-listen-port 选项设置的端口, 未设置则是 6800
普通情况设置为: http://host:port/jsonrpc
使用 --rpc-secret=xxxxxx 选项设置为: http://token:xxxxxx@host:port/jsonrpc
使用 --rpc-user=user --rpc-passwd=pwd 选项设置为: http://user:pwd@host:port/jsonrpc

![](http://oduq3lfcc.bkt.clouddn.com/Aria%E9%85%8D%E7%BD%AE%E5%9B%BE%E7%89%87.png)

# 添加脚本

## 迅雷离线
Chrome Extension: [ThunderLixianAssistant](https://chrome.google.com/webstore/detail/thunderlixianassistant/eehlmkfpnagoieibahhcghphdbjcdmen)
UserScript: [ThunderLixianExporter](https://github.com/binux/ThunderLixianExporter)
## 旋风离线
UserScript: [XuanFengEx](https://greasyfork.org/scripts/354-xuanfengex)
UserScript: [LixianExporter](https://greasyfork.org/scripts/2398-lixianexporter)
## 百度网盘
Chrome Extension: [BaiduExporter](https://chrome.google.com/webstore/detail/mjaenbjdjmgolhoafkohbhhbaiedbkno)
Firefox Addons: [BaiduExporter](https://github.com/acgotaku/BaiduExporter)
UserScript: [BaiduPanDownloadHelper](https://greasyfork.org/scripts/294-baidupandownloadhelper)
## 其他脚本
Chrome Extension: [谷歌浏览器aria2插件](https://chrome.google.com/webstore/detail/nimeojfecmndgolmlmjghjmbpdkhhogl)

# 常见问题
## aria2 不能启动
清空 aria2.session 内容，重新尝试启动。
