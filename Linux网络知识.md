## Linux网络知识预备

### 网络分层的意义

在物理层 不同厂商可能会生产出不一样的接口 不同接口之前是无法通信的 为了让我们的软件具有通用性 就需要在物理层之上再抽象一层 用来统一规范 提高软件兼容性

**物理层：**不同厂商之间的物理连接 产生数据帧 

**数据链路层：**统一的物理连接，封装数据包以及加入MAC地址 解决底层硬件的不兼容 代表：交换机

**网络层：**交换机之间如果想要通信 通过路由 可让多个局域网之间通信 ip  代表：linux服务器

**传输层：**网卡传输到主机上的数据应该给主机上的哪个应用使用（端口号）？ 

​				TCP/UDP 代表：应用开发C++/java

**应用层：**规定传输层传来的格式 HTTP FTP QT 

## 防火墙

规定什么样的数据可以进 什么样的数据可以出 控制网络对特定数据的连通性

物理层加防火墙：拔网线 太麻烦 不灵活

数据链路层加防火墙：直接禁止某些mac地址 不够细致 但是速度很快

网络层+数据链路层：可同时通过mac+ip来设置 灵活 速度快

## 网络操作

### ping

检测网络通不通

```shell
ping www.baidu.com
```

<img src = "http://qkwfs9p87.hn-bkt.clouddn.com/image-20201202215031730.png" style="width:90%"></img>


查看自己的网络配置

<img src="http://qkwfs9p87.hn-bkt.clouddn.com/image-20201202215851167.png" style="width:30%"></img>

<img src="http://qkwfs9p87.hn-bkt.clouddn.com/image-20201202215911405.png" style="width:70%"></img>

<img src="http://qkwfs9p87.hn-bkt.clouddn.com/image-20201202215944015.png" style="width:70%"></img>

然后就可以进行相关的网络设置

当运行服务器版的linux时 则只能用命令来设置了

查看当前网卡信息

```shell
ifconfig
```

![image-20201203105248029](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203105248029.png)

`lo`表示的就是本地回环网络 local的地址就是这样

`Rx` `Tx` 是读写数据包

`wlp`表示无线网卡  `inet`表示自己的`ip`地址 `ether`表示`mac`地址(全球唯一)

```shell
ifconfig -a #显示所有网卡
```

设置网卡信息

```shell
sudo ifconfig wlp5s0 10.161.255.190 netmask 255.255.128.0
#              网卡名       设置的ip    设置的子网掩码 
```

此时网络不通了..因为手动指定了ip地址 所以dhcp协议失效了

![image-20201203105412656](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203105412656.png)

查看网关

```shell
route
```

![image-20201202225005153](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201202225005153.png)

发现没有网关了..

恢复网络（利用dhcp的客户端命令 自动获取ip等）

```shell
sudo dhclient wlp5s0
```

此时就可以正常上网了

此时若查看网关 依然是空 则需要先释放 再获取

```shell
sudo dhclient -r
sudo dhclient wlp5s0
```

再次查看网关和ip

```shell
route
ifconfig
```

发现有ip又变成新的了 也有网关了

![image-20201203111451520](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203111451520.png)



添加一个路由

```shell
route add 目的地址 gw 网关地址
```

### 命令行版的浏览器

**curl是一个命令行工具，通过指定的URL来上传或下载数据，并将数据展示出来。curl中的`c`表示client，而URL，就是URL。这里我们介绍一下curl的使用**。

### 安装`curl`

```shell
sudo apt install curl
```

发现报错

![image-20201203112855517](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203112855517.png)

缺什么就加上什么

```shell
sudo apt install libcurl4=7.68.0-1ubuntu2
sudo apt install curl
```

即可完成安装

体验命令行上打印网址信息

```shell
curl www.baidu.com
```

![image-20201203113201586](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203113201586.png)

保存网页到某个位置

```shell
curl www.baidu.com -o baidu.com
```

![image-20201203113501982](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203113501982.png)

简单来下载网页也可以用(更方便) `curl`更强大

```shell
wget http://www.baidu.com
```

解析网址 获得ip

```shell
host www.baidu.com
```

![image-20201203113741298](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203113741298.png)

### 追踪网络数据包的转发过程

以下命令可以查看到达目的地址 经历了哪些路由

```shell
traceroute 112.80.248.76
```

![image-20201203113903249](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203113903249.png)

因为防火墙和智能路由 可能被过滤掉 不显示ip信息 ***

### 在局域网内追踪 可找到子网拓扑图

例如查看长理官网 经过了2个中间路由转发

![image-20201203131948964](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203131948964.png)

### 显示本机上当前所有连网的本地ip及端口号 以及目标ip端口号

注：端口号443表示https连接

```shell
netstat -nt
```

- -n直接使用IP地址，而不通过域名服务器。
- -t显示TCP传输协议的连线状况。

![image-20201203133019745](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203133019745.png)

实例：例如想知道qq上对方的ip地址 就可以给对方发送了个文件 实时接收的那种（否则只能收到腾讯的服务器ip） 然后运行该命令 就可以找到对方的ip 进而定位对方的位置。

### 抓包

窃取用户数据包 修改后再发出

只要不是https协议（加密）的 都会以明文来显示（实例:让linux笔记本网卡共享热点 无密码 让别人连接 然后运行tcpdump 就可以看到对方的上网信息 如账号密码等）

```shell
sudo tcpdump -i wlp5s0
```

![image-20201203134530290](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203134530290.png)

此时将不断监听上网信息 可做**中间人攻击**

监控所有的80端口(web网页服务)的信息

```shell
sudo tcpdump -i wlp5s0 -nnA 'port 80'
```

-i ：指定tcpdump需要监听的接口。默认会抓取第一个网络接口

-n：对地址以数字方式显式，否则显式为主机名，也就是说-n选项不做主机名解析。

-nn：除了-n的作用外，还把端口显示为数值，否则显示端口服务名。

![image-20201203204646829](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203204646829.png)

因为是https 所以是乱码 可以监听这个 http://yuansasi.com/ ip为119.29.101.183

```
 sudo tcpdump -i wlp5s0 net 119.29.101.183
```

![image-20201203205732565](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203205732565.png)

### 查看端口占用情况

查看80端口占用情况

```shell
netstat -apn | grep 80
```

```shell
ps -aux | grep 80
```

**实例:**当某些服务起不动时,可以运行上述命令 进而可以查找到进程id号 去杀死占用该端口号的进程

### 查看本地网络配置

```shell
cat /etc/hosts
```

![image-20201203210523700](http://qkwfs9p87.hn-bkt.clouddn.com/image-20201203210523700.png)

### host解析-修改主机名

```shell
sudo vim /etc/hostname
sudo vim /etc/hosts
```

vim下显示行号 : set nu

   yy复制该行 p粘贴

4 yy 表示复制四行 

gg 第一行 G最后一行 

3 ↑ 向上移动三行 4 ↓ 向下移动四行

dd删除该行 7 dd 删除7行