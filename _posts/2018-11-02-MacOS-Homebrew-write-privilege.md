---
layout:     post
title:      "MacOS Homebrew 关于/usr/local写入权限无法修改的问题解决记录"
subtitle:   "MacOS Homebrew"
date:       2018-11-02 12:00:00 +0800
author:     "Fa1ConZ"
header-img: "img/post-bg-2015.jpg"
tags:
    - MacOS
    - Homebrew
---

## 问题
在使用brew命令的时候，会遇到错误提示
`Error:/usr/local is not writable.`

并建议执行命令：
`$ sudo chown -R $(whoami) /usr/local`

执行权限修改命令，系统会提
`chown: /usr/local: Operation not permitted`

现在问题就卡住了，因为执行`brew update`之类的命令，需要对`/usr/local`进行写入操作。但是操作用户无法像对普通文件夹操作一样，通过`chown`获得`write`权限。由于`/usr/local`是系统文件夹，macOS限制了对其的操作权限。

归根到底，是下面的机制在作怪：

`Rootless`

苹果从 `OS X El Capitan 10.11` 系统开始使用了 `Rootless` 机制，可以将该机制理解为一个更高等级的系统的内核保护措施，系统默认将会锁定 `/system`、`/sbin`、`/usr` 这三个目录。

## 问题解决

找到问题所在，处理就方便了。

在终端输入

`$ csrutil status`

收到系统提示

`System Integrity Protection status：enabled`

说明`Rootless`默认打开，此时无法通过`sudo`命令，对`/system`、`/sbin`、`/usr` 这三个目录进行修改。

### 打开、关闭Rootless机制

1. 重启 Mac
2. 开机时后按下 Command+R，进入恢复模式。
3. 在上面的菜单实用工具中找到并打开 Terminal
4. 输入命令： `$ csrutil disable`
5. 此时rootless已经关闭，退出恢复模式，正常进入系统。


在终端输入

`$ csrutil status`

收到系统提示

`System Integrity Protection status：disabled`

rootless已关闭，可通过

`$ sudo chown -R $(whoami) /usr/local`

修改local权限。

## 后记

建议修改完成之后，为了系统安全，将rootless重新开启。开启方式同关闭方式一样，在恢复模式的Terminal输入：

`$ csrutil enable`

邮箱：<falcon.zjx1225@gmail.com>

欢迎转载，请注明出处，谢谢。
