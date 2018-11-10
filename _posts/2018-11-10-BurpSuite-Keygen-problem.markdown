---
layout:     post
title:      "BurpSuite-关于破解工具KeyGen无法使用的问题解决记录"
subtitle:   "BurpSuite"
date:       2018-11-10 12:00:00 +0800
author:     "Fa1ConZ"
header-img: "img/post-bg-2015.jpg"
tags:
    - BurpSuite
---


## 前言
- 环境：

```kali 2018.2```

- 系统源：
```
deb http://mirrors.aliyun.com/kali kali-rolling main non-free contrib 
deb-src http://mirrors.aliyun.com/kali kali-rolling main non-free contrib
```
- 通过源更新系统内环境及工具（截止2018.11.1)
```shell
apt-get update
apt-get upgrade
apt-get dis-upgrade
```

由于BurpSuite Pro功能需要付费，所以为了学习高阶功能，需要使用破解版。但在我使用破解工具的过程中，无论是版本是否对应，运行工具都会出现以下问题：
- 点击Keygen工具中的run按钮无效。
- 直接打开BurpSuite，输入工具中的License，显示`The supplied license key was not recognized`，提示License无效。

![image](http://note.youdao.com/yws/res/13609/6B26E55AD4E247DF8744B4088BEE4ED4)

## 正文
关于以上问题，查了很多资料都没有具体说法，决定自己分析一波。

首先直接将命令行语句放入bash中运行：
![image](http://note.youdao.com/yws/res/13633/649D6B81F7404C39B3E17012108764C4)
系统提示`-Xbootclasspath/p`不再作为参数，那么主要问题应该出在`-Xbootclasspath/p`参数上，没学过Java的渣渣只能先找一下有没有其他替换的办法。

关于`-Xbootclasspath`的资料相对较少，但是最靠得住的还是官方：
`https://www.ibm.com/support/knowledgecenter/en/SSYKE2_8.0.0/openj9/xbootclasspath/index.html`
![image](http://note.youdao.com/yws/res/13636/BA607CCEFCEE415E97D17BD57F4C4C98)

通过官方说明发现了问题，`-Xbootclasspath:<path>`和`-Xbootclasspath/p:<path>`仅限到Java 8，更新后的kali使用`JDK 11`。
![image](http://note.youdao.com/yws/res/13667/A36ED6DD22EA49E0955BED8F0B1AD0A2)


## 解决问题
问题解决办法：降版本运行。

1. 找到低版本jdk执行文件。
> find / -name java
![image](http://note.youdao.com/yws/res/13678/98891D8E598F492EAEBB19667B7934FD)

2. 直接用jdk 8的执行文件路径替换java命令（没必要修改PATH，以免出现其他软件的问题）。
> /usr/lib/jvm/java-8-openjdk-amd64/bin/java -Xbootclasspath/p:burp-loader-keygen.jar -jar burpsuite_pro_v1.7.32.jar

3. 正常执行后续破解工作。


## 结尾

该工具有个使用不方便的地方就是，在第一次运行输入License破解后，每次使用都需要先打开它去Run那一条命令。

所以直接自建shell脚本`run.sh`，保存在同目录下：
```
#!/bin/bash
/usr/lib/jvm/java-8-openjdk-amd64/bin/java -Xbootclasspath/p:burp-loader-keygen.jar -jar burpsuite_pro_v1.7.32.jar
```

每次要打开BurpSuite，只用在该目录下运行
> ./run.sh
---

关于版本问题，如果有Java大佬有更好的解决方案，欢迎提出建议。

邮箱：<falcon.zjx1225@gmail.com>

欢迎转载，请注明出处，谢谢。
