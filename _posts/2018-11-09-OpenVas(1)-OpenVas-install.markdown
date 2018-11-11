---
layout:     post
title:      "OpenVas(1)-OpenVas安装"
subtitle:   "OpenVas"
date:       2018-11-09 12:00:00 +0800
author:     "Fa1ConZ"
header-img: "img/post-bg-2015.jpg"
tags:
    - OpenVas
---

参考文档：http://blog.51cto.com/linhong/2134910?source=drh

---
[ TOC ]

---
# 安装环境

- MacOS 10.14
- VMware Funsion 8
- 4.18.0-kali2-amd64

## 替换源
kali自带源国内访问比较慢，所以更新为国内aliyun的镜像源。

> vim /etc/apt/sources.list

将其余部分都注释掉，在尾部添加以下部分：
```
 deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib 
 deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
```

## 更新

> apt-get clean

下面三条命令多执行几次，对系统自带软件进行更新。

> apt-get update 

> apt-get upgrade 

> apt-get dist-upgrade



# 安装OpenVas

新版本的kali系统并没有自带OpenVas，所以需要手动进行安装：

## OpenVas安装包及依赖环境下载

> apt-get install openvas*

涉及依赖环境较多，且安装包较大，所以需要等待较长时间。

## 初始化OpenVas

> openvas-setup

![image](http://note.youdao.com/yws/res/13257/F5AC90BB4A4C4577BAF5E853F75D0E36)
 
![image](http://note.youdao.com/yws/res/13259/4B0232B1A31848D090C76F86D8C70120)

![image](http://note.youdao.com/yws/res/13261/8D3BB62CA24D4B95B459CBAE9E0B8009)

初始化进程总共分为三步，时间较长，我们只需要了解最后的密码

![image](http://note.youdao.com/yws/res/13265/E5C5BBB289B643809FA21E9477D6B7C1)


初始化完成后，建议运行自带的检查命令检查一下设置情况。

> openvas-check-setup

![image](http://note.youdao.com/yws/res/13272/682CE2D0A0A243B98C1DC962D93ACB16)

看到 `It seems like your OpenVAS-9 installation is OK.` 就没问题了。

## OpenVas配置
将以下文件中的参数地址由127.0.0.1替换为0.0.0.0
- /lib/systemd/system/greenbone-security-assistant.service
    - --listen、--mlisten
- /lib/systemd/system/openvas-manager.service
    - --listen
- /etc/default/openvas-manager 
    - MANAGER_ADDRESS
- /etc/default/greenbone-security-assistant 
    - GSA_ADDRESS
    - MANAGER_ADDRESS

若需通过远程访问本机，需要增加host 头主机地址（IP或域名）
在`/lib/systemd/system/greenbone-security-assistant.service`文件中的`mlisten`参数后增加
> --allow-header-host=外部访问的地址IP或域名

本机通过MacOS访问虚拟机中的kali，将kali的IP指定即可。

配置完成后，重启OpenVas：
```
> openvas-stop
> systemctl daemon-reload
> openvas-start
# 安装完整性检测
> openvas-check-setup
```

## 登录操作页面

如果安装过程中操作失误未记录账号密码，可通过以下口令获得账户名，并重置密码。
- 获取账户名
> openvasmd --get-users

![image](http://note.youdao.com/yws/res/13302/D194EFE73C414212B5BC553FF112BF87)

- 重置密码
> openvasmd --user=admin --new-password=new_password


登录
访问前，关闭本地代理和防火墙，并在浏览器确认未授权证书通过。
- 本地登录
![image](http://note.youdao.com/yws/res/13308/2832B98FBD854E85B716A71473348755)

- 远程登录
![image](http://note.youdao.com/yws/res/13311/89A4D6B1DD3E4BB7B149020BE200711E)


## 后记

邮箱：<falcon.zjx1225@gmail.com>

欢迎转载，请注明出处，谢谢。