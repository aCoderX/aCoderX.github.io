---
title: ubuntu16.04常用软件的安装
date: 2017-03-26 16:48:33
tags: linux
---
### 常用软件

#### chrome
1. 下载deb包
```
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```
2. 安装
```
sudo dpkg -i google-chrome-stable_current_amd64.deb 
```
<!--more-->

#### 网易云音乐
1. 在官网下载deb安装包
2. 安装时发现缺少一些依赖
3. 安装依赖后再次尝试安装即可
```
sudo apt-get install libqt5x11extras5
...
```

#### 微信
electronic wechat
* 在https://github.com/geeeeeeeeek/electronic-wechat/releases直接下载安装包，解压

* 添加快捷方式
```
sudo gedit /usr/share/applications/webchat.desktop
```
```
[Desktop Entry]
Encoding=UTF-8
Name=electronic-wechat
Comment=electronic-wechat
Exec=/home/xudi/soft/other/electronic-wechat-linux-x64/electronic-wechat //不能使用~
Icon=/home/xudi/soft/other/electronic-wechat-linux-x64/wechat.png
Terminal=false
StartupNotify=true
Type=Application
Categories=Application
```

#### shutter（截图工具）
```
sudo add-apt-repository ppa:shutter/ppa
sudo apt-get update
sudo apt-get install shutter
```
* 添加快捷键
设置->键盘->自定义快捷键->添加Shutter Select、shutter -s

#### ss
1. 下载shadowsocks-qt5并配置服务器的地址、端口、密码等
```
sudo add-apt-repository ppa:hzwhuang/ss-qt5
sudo apt-get update
sudo apt-get install shadowsocks-qt5
```
2. chrome使用SwitchyOmega插件进行代理
* 从Github上直接下载最新版 https://github.com/FelisCatus/SwitchyOmega/releases/ ，然后浏览器地址打开chrome://extensions/ ，将下载的插件托进去安装。
* 新建情景模式->代理协议：socket5，代理服务器：127.0.0.1，代理端口1080->保存
* 点击右上角多出来的圆的图标，选择代理，就可以上google啦


#### 搜狗输入法
1. 在搜狗官网下载deb安装包
2. 运行sudo dpkg -i sogoupinyin_2.0.0.0078_amd64.deb 发现缺少依赖包
3. 执行 sudo apt-get -f install
4. 运行sudo dpkg -i sogoupinyin_2.0.0.0078_amd64.deb 安装成功
5. 重启发现可以使用

* 有的时候会发现搜狗打不出中文
> 删除配置文件，重启搜狗
ubuntu下搜狗的配置文件在 ~/.config下的3个文件夹里：
SogouPY、SogouPY.users、sogou-qimpanel
删除这3个文件夹，然后重启搜狗。

#### docky
```
sudo add-apt-repository ppa:ricotz/docky
sudo apt-get update
sudo apt-get install docky
```
如果要卸载使用
```
sudo apt-get remove docky
```
完成之后可以把自带的侧边栏隐藏 设置->外观->行为->自动隐藏启动器打开

#### Unity Tweak Tool
```
sudo apt-get install unity-tweak-tool 
```
设置类似mac的主题
```
sudo add-apt-repository ppa:noobslab/macbuntu
sudo apt-get install  macbuntu-os-ithemes-lts-v7 
sudo apt-get install  macbuntu-os-icons-lts-v7 
```

### 程序猿

#### jdk
1. 下载安装jdk
* 在官网下载jdk-8u121-linux-x64.tar.gz
* 解压到相应目录
2. 配置环境变量
编辑～/.bashrc文件，增加
```
JAVA_HOME=~/soft/java/jdk1.8.0_121
JRE_HOME=$JAVA_HOME/jre
JAVA_BIN=$JAVA_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME PATH CLASSPATH
```
3. 为了让更改立即生效
```
source ~/.bashrc
```
4. java -vesion 测试是否安装成功

#### git
1. 安装git
```
sudo apt-get install git-core
```
2. 初始化全局变量
```
git config --global user.name "namei"
git config --global user.email "email"
```
3. 创建公钥
```
ssh-keygen -C 'you email address@gmail.com' -t rsa
```
会在~/.ssh/下创建相应密钥
4. 上传公钥
在github密钥管理中添加~/.ssh/id_rsa.pub中的内容
5. 测试
```
ssh -v git@github.com
```

#### maven
1. 在官网下载apache-maven-3.3.9.bin.tar.gz
2. 解压并移动到想要存放的地方
```
tar -zxvf apache-maven-3.3.9.bin.tar.gz
mv apache-maven-3.3.9 ~
```
3. 配置环境变量
```
sudo gedit /etc/profile
```
增加
```
#set maven environment
export M2_HOME=~/apache-maven-3.3.9
export MAVEN_OPTS="-Xmx1024m"                          #避免内存溢出错误（可选）
export PATH=${M2_HOME}/bin:${PATH}
```
4. 测试
```
source /etc/profile
mvn -v
```
5. 配置本地库路径
在apache-maven-3.3.9/conf/setting.xml中加入
```
<localRepository>~/maven/repository</localRepository>
```
6. 配置镜像

```
    <mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
    </mirror>
```

#### IDEA
1. 官网下载压缩包
2. 解压，运行bin目录下的idea.sh开始安装
```
sh idea.sh
```

#### sublime text 3
```
sudo add-apt-repository ppa:webupd8team/sublime-text-3
sudo apt-get update 
sudo apt-get install sublime-text-installer
```

#### vim
* 安装
```
sudo apt-get install vim-gtk
```
* 配置
```
sudo vim /etc/vim/vimrc
```
在最后添加
```
set nu                           // 在左侧行号
set tabstop                  //tab 长度设置为 4
set nobackup               //覆盖文件时不备份
set cursorline               //突出显示当前行
set ruler                       //在右下角显示光标位置的状态行
set autoindent             //自动缩进
```

#### nodejs
1. 官网下载压缩包node-v6.10.1-linux-x64.tar.xz
2. 解压
```
tar -xJf node-v6.10.1-linux-x64.tar.xz
```
3. 移到通用的软件安装目录 /opt/
```
sudo mv node-v6.10.1-linux-x64 /opt/
```
4.设置环境变量
在/etc/profile中增加
```
export NODE_HOME=/opt/node-v6.10.1-linux-x64
export PATH=$PATH:$NODE_HOME/bin 
export NODE_PATH=$NODE_HOME/lib/node_modules
```
5. 验证
```
node -v
npm -v
```
6.使用淘宝源

在~/.bashrc中添加
```
alias cnpm="npm --registry=https://registry.npm.taobao.org \
--cache=$HOME/.npm/.cache/cnpm \
--disturl=https://npm.taobao.org/dist \
--userconfig=$HOME/.cnpmrc"
```
source ~/.bashrc

cnpm -v