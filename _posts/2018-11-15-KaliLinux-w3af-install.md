---
layout:     post
title:      "Kali Linux 2018.2 安装W3AF"
subtitle:   "W3AF install"
date:       2018-11-15 12:00:00 +0800
author:     "Fa1ConZ"
header-img: "img/post-bg-2015.jpg"
tags:
    - Kali Linux
    - W3AF
---

环境：
Kali 2018.2
python2.7

由于Kali Linux 2018.2版本不再自带W3AF工具，所以需要自行从网上进行源码安装。

1. 首先下载源码到`/usr/local/src`目录
```
cd /usr/local/src
git clone https://github.com/andresriancho/w3af.git
```

2. 更新源和pip
```
apt-get update
apt-get dis-upgrade
pip install --upgrade pip
```

3. 安装pybloomfiltermmp
```
pip install pybloomfiltermmap
```

4. 修改W3AF配置文件。
```
#修改requirements.py文件相关配置
vim w3af/w3af/core/controllers/dependency_check/requirements.py

PIPDependency('pybloomfilter', 'pybloomfiltermmap', '0.3.15'),
PIPDependency('OpenSSL', 'pyOpenSSL', '16.2.0'),
PIPDependency('lxml', 'lxml', '4.2.5'),

#修改mac.py
vim w3af/core/controllers/dependency_check/platforms/mac.py

MAC_CORE_PIP_PACKAGES.remove(PIPDependency('pybloomfilter', 'pybloomfiltermmap', '0.3.15')

```


5. 执行`./w3af_gui`，系统提示需要的依赖库，并在`/tmp`生成安装依赖的脚本`w3af_dependency_install.sh`。

6. 运行`./w3af_dependency_install.sh`。
```
cd /tmp
./w3af_dependency_install.sh
```

7. 提示缺少npm，安装npm
```
apt-get install npm
```

8. 再次运行`./w3af_dependency_install.sh`
```
./w3af_dependency_install.sh
```

9. 执行`./w3af_console`，系统提示需要的依赖库，进行补充安装。
```
cd /usr/local/src/w3af/w3af
./w3af_console
```

10. 系统已可正常执行`./w3af_console`，但是执行`./w3af_gui`时提示缺少`webkit`库，所以需要安装`webkit`库。pip和源中都已经不包含`webkit`库及相关依赖库，需要进行源码安装。

(1). 下载deb包：
```
wget http://ftp.br.debian.org/debian/pool/main/p/pywebkitgtk/python-webkit_1.1.8-3_amd64.deb
wget http://ftp.br.debian.org/debian/pool/main/w/webkitgtk/libjavascriptcoregtk-1.0-0_2.4.11-3_amd64.deb
wget http://ftp.br.debian.org/debian/pool/main/w/webkitgtk/libwebkitgtk-1.0-0_2.4.11-3_amd64.deb
wget http://ftp.br.debian.org/debian/pool/main/p/python-support/python-support_1.0.15_all.deb
```

(2). 安装依赖和软件包，务必按顺序进行安装。
```
dpkg -i libjavascriptcoregtk-1.0-0_2.4.11-3_amd64.deb
dpkg -i python-support_1.0.15_all.deb
dpkg -i libwebkitgtk-1.0-0_2.4.11-3_amd64.deb
dpkg -i python-webkit_1.1.8-3_amd64.deb
```

11. 安装完成，已可正常运行`w3af_console`和`w3af_gui`，为方便执行，将两个执行路径添加至`alias`。
```
vim /root/.bashrc
:/alias
在下面段添加w3af_gui和w3af_console命令。
if [ -x /usr/bin/dircolors ]; then
    test -r ~/.dircolors && eval "$(dircolors -b ~/.dircolors)" || eval "$(dircolors -b)"
    alias ls='ls --color=auto'

    alias w3af_gui='/usr/local/src/w3af/w3af_gui'
    alias w3af_console='/usr/local/src/w3af/w3af_console'

fi
```

---
后记

由于环境不一致，网上能搜到的安装方法顺序均不一致。不过大致上就一个思路，通过运行`./w3af_console`和`./w3af_gui`，根据提示安装依赖库即可。

ps：可能由于网络波动原因或者系统临时配置引起的问题，多次重复执行命令和重启可以解决大部分库安装异常。