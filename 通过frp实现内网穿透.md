# 通过frp实现内网穿透

## 需求

实现内网穿透，能够外网远程桌面 Windows 电脑

## 前提条件

一台 Windows 电脑，我的系统是 Windows 10_2004

一台云服务器，拥有公网IP地址，我的系统是 CentOS 7.6

```
[root@ntbgy ~]# cat /etc/redhat-release 
CentOS Linux release 7.6.1810 (Core)
```

*不确定系统是否有影响，仅列出以供参考*

## 实现步骤

### 下载frp软件

GitHub地址：

https://github.com/fatedier/frp/releases

我这里是在宝塔面板创建的ftp目录里下载，Windows和Linux的我都下载了，版本你可自行选择

```
[root@ntbgy ~]# cd /www/wwwroot/ftp/
[root@ntbgy ftp]# wget https://github.com/fatedier/frp/releases/download/v0.33.0/frp_0.33.0_linux_amd64.tar.gz
[root@ntbgy ftp]# wget https://github.com/fatedier/frp/releases/download/v0.33.0/frp_0.33.0_windows_amd64.zip
```

下载官方文档：

我这里是在Windows电脑里下载的，也可网页直接查看中文文档，README_zh.md

```
git clone https://github.com/fatedier/frp.git
```

### Linux服务器端配置

首先创建安装目录，然后将文件复制过去解压，原文件建议先保留。frpc开头的是frp客户端相关的文件，我这里删除了，也可保留。

```
[root@ntbgy ~]# mkdir /var/frps
[root@ntbgy ~]# cp /www/wwwroot/ftp/frp_0.33.0_linux_amd64.tar.gz /var/frps/
[root@ntbgy ~]# cd /var/frps/
[root@ntbgy frps]# ls
frp_0.33.0_linux_amd64.tar.gz
[root@ntbgy frps]# tar -xzvf frp_0.33.0_linux_amd64.tar.gz 
[root@ntbgy frps]# ls
frp_0.33.0_linux_amd64  frp_0.33.0_linux_amd64.tar.gz
[root@ntbgy frps]# rm -f frp_0.33.0_linux_amd64.tar.gz 
[root@ntbgy frps]# mv frp_0.33.0_linux_amd64/* .
[root@ntbgy frps]# rm -rf frp_0.33.0_linux_amd64/
[root@ntbgy frps]# ls
frpc  frpc_full.ini  frpc.ini  frps  frps_full.ini  frps.ini  LICENSE  systemd
[root@ntbgy frps]# rm -rf frpc*
[root@ntbgy frps]# ll
total 12700
-rwxrwxr-x 1 www www 12976128 Apr 27 16:59 frps
-rw-rw-r-- 1 www www     4639 Apr 27 17:06 frps_full.ini
-rw-rw-r-- 1 www www       26 Apr 27 17:06 frps.ini
-rw-rw-r-- 1 www www    11358 Apr 27 17:06 LICENSE
drwxrwxr-x 2 www www     4096 Apr 27 17:06 systemd
[root@ntbgy frps]# 
```

frps.ini和frps_full.ini都是服务器端配置文件，我这里使用frps.ini配置文件，因为简单，想要实现更多功能请使用frps_full.ini配置文件，并参考官方中文文档进行修改。frps.ini默认配置如下：

```
[root@ntbgy frps]# cat frps.ini 
[common]
bind_port = 7000
```

我这里添加了一个token，其它不做任何修改

```
bind_port = 7000
#bind_port 与客户端绑定的进行通信的端口

# auth token
token = 110110121
#token 一串字符串，以作客户端进行请求的一个令牌，可理解为暗号
```

#### 启动Linux服务器端服务

./frps -c ./frps.ini是前台启动

nohup ./frps -c ./frps.ini &是后台启动

```
[root@ntbgy frps]# ./frps -c ./frps.ini
2020/07/05 16:29:24 [I] [service.go:178] frps tcp listen on 0.0.0.0:7000
2020/07/05 16:29:24 [I] [root.go:209] start frps success
^C
[root@ntbgy frps]# nohup ./frps -c ./frps.ini &
[1] 15110
[root@ntbgy frps]# nohup: ignoring input and appending output to ‘nohup.out’

[root@ntbgy frps]# 

```

服务器基本不会重启，这样设置基本也够了，但我就想设置开机自启怎么办？建议自己百度解决，哈哈哈哈，博主目前还没有验证。

### Windows客户端配置

将下载好的frp_0.33.0_windows_amd64.zip解压，让后放在你想要安装的目录，我这里放在C:\frpc，嗯，我把文件夹名字也改了，也可不改。frps开头的是服务器端需要的文件，可删除。修改配置文件frpc.ini，建议使用notepad++进行修改。

> https://www.liaoxuefeng.com/wiki/1016959663602400/1017024645952992
>
> 请注意，*不要用Word和Windows自带的记事本*。Word保存的不是纯文本文件，而记事本会自作聪明地在文件开始的地方加上几个特殊字符（UTF-8 BOM），结果会导致程序运行出现莫名其妙的错误。

修改配置如下：

```
[common]
#common 通用配置，名字你可以修改
server_addr = 127.0.0.1
#server_addr 你的服务器公网IP地址
server_port = 7000
#server_port 的值需要和bind_port（与客户端绑定的进行通信的端口）的值相同
#前面服务器端做了token，故添加一行
# for authentication
token = 110110121

[mstsc]
#我这里主要是见名知意，名字你可以修改
type = tcp
#协议类型为tcp，这里不做修改
local_ip = 127.0.0.1
#内网服务器ip，这里就做本机的内网穿透，故不做修改
local_port = 3389
#修改为微软远程桌面默认端口
remote_port = 6000
#自定义的访问内部mstsc端口号，可修改
```

*如果你内网部署了web服务，VMware建了虚拟机（该虚拟机网卡选择桥接），也想直接内网穿透，那么可以在文档后面添加下列内容：*

```
[tomcat]
type = tcp
local_ip = 127.0.0.1
local_port = 8080
#tomcat默认端口
remote_port = 6002

[ssh_local_centos]
type = tcp
local_ip = 172.16.5.176
local_port = 22
#ssh协议默认端口
remote_port = 6003
```



#### 小贴士

客户端配置就是完成了，但是先不急着启动，你需要在服务器端防火墙放行对应端口，同时需要注意的是，若你是阿里云或腾讯云服务器还需要设置对应安全组，安全组设置可自行参考官方文档，这里列出如何放行防火墙端口号。

```
server_port = 7000
local_port = 3389
remote_port = 6000
```

查看已经放行的端口号

```
firewall-cmd --permanent --zone=public --list-ports
```

放行端口号，出现” success” 则表示添加成功

```
firewall-cmd --zone=public --add-port=6000/tcp --permanent
firewall-cmd --zone=public --add-port=7000/tcp --permanent
firewall-cmd --zone=public --add-port=3389/tcp --permanent
```

重启防火墙使其立即生效，出现” success“ 字样则表示重新启动成功

```
firewall-cmd --reload
```

再次查看是否已经放行

```
firewall-cmd --permanent --zone=public --list-ports
```

或者验证端口是否生效，出现 ” yes “字样则代表生效

```
[root@ntbgy frps]# firewall-cmd --zone=public --query-port=6000/tcp
yes
[root@ntbgy frps]# firewall-cmd --zone=public --query-port=7000/tcp
yes
[root@ntbgy frps]# firewall-cmd --zone=public --query-port=3389/tcp
yes
```

#### 启动Windows客户端服务

方法一、打开cmd.exe终端，进入对应安装目录执行

WIN+R，键入cmd，并确定

```
cd c:/frpc
dir
#显示当前文件夹文件
frpc.exe -c frpc.ini
```

方法二、编写.bat脚本，建议使用绝对路径

```
c:/frpc/frpc.exe -c c:/frpc/frpc.ini
```

方法三、前面两种方法每次重启电脑都需要再次执行一次，而且cmd.exe窗口还会一直显示在前台，不是特别方便。

> https://blog.csdn.net/a873744779/article/details/102933229
>
> 2、设置frpc自启动，自启动脚本如下
>
> ```
> Set ws = CreateObject("Wscript.Shell") 
>    ws.run "cmd /c c:\frpc\frpc.exe -c c:\frpc\frpc.ini",vbhide
> ```
>
> 然后把vbs放到启动目录即可
>
> ```
> C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp
> ```

亲测此方法有效，我重启后（不知不重启可不可以），可在设置-应用-启动查看到相关条目

## 远程连接测试

先查看一下系统是否允许远程连接

控制面板-->所有控制面板项-->系统-->高级系统设置-->远程

![image-20200705175300035](C:\Users\汪超\AppData\Roaming\Typora\typora-user-images\image-20200705175300035.png)

这个6000来源于客户端配置文件frpc.ini

```
remote_port = 6000
#自定义的访问内部ssh端口号，可修改
```



## 我遇到的问题

#### 最简单的配置，最直接的success，但是就是无法远程连接

frpc.ini

```
[common]
server_addr = XXX.XXX.XXX.XXX
server_port = 7000

[ssh]
type = tcp
local_ip = 127.0.0.1
local_port = 22
remote_port = 6000
```

frps.ini

```
[common]
bind_port = 7000
```

原因，害

```
[mstsc]
#这里也修改一下，见名知意
type = tcp
local_ip = 127.0.0.1
local_port = 3389
#协议这里错误，应该改为window远程桌面3389端口
remote_port = 6000
```



#### token报错问题

```
c:\frpc>frpc.exe -c frpc.ini
2020/07/05 18:23:52 [E] [service.go:273] token in login doesn't match token from configuration
2020/07/05 18:23:52 [W] [service.go:101] login to server failed: token in login doesn't match token from configuration
token in login doesn't match token from configuration
```

网上告知是多半是服务器端和客户端token的值不相同，但的确是相同的，我的配置如下

frps.ini

```
[common]
bind_port = 7000

# auth token
token = 110110121

```

frpc.ini

```
[common]
server_addr = XXX.XXX.XXX.XXX
server_port = 7000

[mstsc]
type = tcp
local_ip = 127.0.0.1
local_port = 3389
remote_port = 6000
# for authentication
token = 110110121
```

解决办法1，不配置token

解决办法2，查看官方中文文档

> 原文档关于token的说明
>
> #### Token
>
> 当 `authentication_method = token`，将会启用基于 token 的验证方式。
>
> 需要在 `frpc.ini` 和 `frps.ini` 的 `[common]` section 中设置相同的 `token`。

即 frpc.ini 修改为：

```
[common]
server_addr = XXX.XXX.XXX.XXX
server_port = 7000
# for authentication
token = 110110121

[mstsc]
type = tcp
local_ip = 127.0.0.1
local_port = 3389
remote_port = 6000
```

## 主要参考资料

frp官方中文文档

https://github.com/fatedier/frp/blob/master/README_zh.md

Windows客户端内网穿透工具frp设置开机自启动

https://blog.csdn.net/a873744779/article/details/102933229

十分钟教你配置frp实现内网穿透

https://blog.csdn.net/u013144287/article/details/78589643/
