---
title: 使用amazon的ec2搭建shadowsocks
date: 2017-03-24 14:25:21
tags: 其他
---
### 启动EC2服务器
利用国外免费的虚拟主机搭建服务器,在服务器上架自己的梯子.

我们这里使用amazon的aws，主要因为它还算(bu)稳(yao)定(qian)，速度不是很快，但是上上google还是没问题的。
 <!--more-->
1. 注册amazon，注意要确保填写的信息通过验证，尤其是支付方式，我当时绑的是信用卡，扣了1刀的预扣款（手欠绑定了两次抠了2刀:(），这个应该只是预扣款，确保这张卡是可用的，之后应该会归还的。
2. 注册完之后，应该就能看到控制台了，选择EC2，点击启动实例，找到一个免费的ubuntu（只有一部分是在12个月免费使用期内的，一定要注意），保存好秘钥（之后用于连接上实例的），然后启动实例。这样你的服务器就在远程开机了，之后就可以看到远程服务器的ip和DNS。

PS:一定要注意的就是12个月的免费服务也是有限制的，对于实例最好只启动一个，因为不仅对流量有限制15G，对开机时间也有限制，不过对于单个实例来说每个月750小时是用不完的，还有其他的限制可以查看amazon的说明。

流量时长等使用情况可以在右上角查询：欢迎使用AWS->我的账单控制面板->账单->全部展开

### 配置服务器的ss
#### 使用xshell登录远程服务器
1. 工具->用户密钥管理者->导入->选择启动实例的时候保存的密钥。
2. 新建连接->连接->主机中填入服务器ip->用户身份验证->方法：public key，用户名root，用户密钥选择刚刚导入的
3. 连接之后，会发现会有提示："Please login as the user "XXX" rather than the user "root"
4. 编辑刚刚的连接把用户名换成上面的XXX，再连接就可以成功了。

#### 安装ss服务端
1. sudo -s （切换到root用户）
2. apt-get update （更新包）
3. apt-get install python-pip
4. apt-get install shadowsocks （安装ss）
5. vi /etc/shadowsocks.json
6. 在shadowsocks.json中配置
```
{
    "server":"0.0.0.0",
    "server_port":端口,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"密码",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
}
```
保存退出
7. ssserver -c /etc/shadowsocks.json -d start （运行ss）
8. vi /etc/rc.local
在第二行加入
```
ssserver -c /etc/shadowsocks.json -d start
```
保存退出，用来添加启动项

9. 设置入站规则
* 在amazon网页上的控制台找到EC2启动的那个实例，点击安全组->编辑入站规则->增加规则，端口选择刚刚json中配置的端口，保存。


### 安装ss客户端
#### windows
* 在网上下载ss的客户端shadowsocks,将刚刚服务器的地址、端口、密码等配置一下，点击启用系统代理。
* 最好选择PAC模式：智能代理，通过PAC文件选择哪些网站需要代理。

#### ubuntu
1. 下载shadowsocks-qt5并配置服务器的地址、端口、密码等
2. chrome使用SwitchyOmega插件进行代理
* 从Github上直接下载最新版 https://github.com/FelisCatus/SwitchyOmega/releases/ ，然后浏览器地址打开chrome://extensions/ ，将下载的插件托进去安装。
* 新建情景模式->代理协议：socket5，代理服务器：127.0.0.1，代理端口1080->保存
* 点击右上角多出来的圆的图标，选择代理，就可以上google啦
