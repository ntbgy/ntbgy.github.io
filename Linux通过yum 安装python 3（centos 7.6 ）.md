# Linux通过yum 安装python 3（centos 7.6 ）

想要做自动化测试就得会脚本语言，比如python，那就学呗。工欲善其事，必先利其器，先把软件安装上！

> 在Linux上安装Python
> 如果你正在使用Linux，那我可以假定你有Linux系统管理经验，自行安装Python 3应该没有问题，否则，请换回Windows系统。对于大量的目前仍在使用Windows的同学，如果短期内没有打算换Mac，就可以继续阅读以下内容。
>
> https://www.liaoxuefeng.com/wiki/1016959663602400/1016959856222624

我的Linux版本

```
[ntbgy@ntbgy ~]$ cat /etc/redhat-release 
CentOS Linux release 7.6.1810 (Core) 
```

## 1.安装依赖

```
[ntbgy@ntbgy ~]$ sudo yum install epel-release -y
```

## 2.安装python 3

```
[ntbgy@ntbgy ~]$ sudo yum install -y python3
```

查看

```
[ntbgy@ntbgy ~]$ python3
Python 3.6.8 (default, Apr  2 2020, 13:34:55) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-39)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

> 执行python时显示版本还是2.7，这是因为为了多版本兼用/usr/bin/python一般是链接文件，链接到所用版本的文件，如原版执行文件是/usr/bin/python2.7，通过python链接到python2.7，这样同时存在高低版本也不会产生文件冲突的问题。而yum安装时是不会改变它的链接目标的，因此直接调用python是相当于还是调用python2.7。因此需要手动更改为链接python3.6。
>
> https://blog.csdn.net/qq_39719589/article/details/84136365

```
[ntbgy@ntbgy ~]$ python
Python 2.7.5 (default, Jun 20 2019, 20:27:34) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-36)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

## 3.修改链接文件

```
[ntbgy@ntbgy bin]$ pwd
/usr/bin
[ntbgy@ntbgy bin]$ ls | grep python
btpython
python
python2
python2.7
python2.7-config
python2-config
python3
python3.6
python3.6m
python-config
[ntbgy@ntbgy bin]$ sudo rm python
[ntbgy@ntbgy bin]$ sudo ln -s python3.6 python
[ntbgy@ntbgy bin]$ 
```

验证

```
[ntbgy@ntbgy bin]$ python
Python 3.6.8 (default, Apr  2 2020, 13:34:55) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-39)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> 
```

## 补充

sudo 是因为我是用ntbgy用户进行安装、配置操作的

> Linux sudo命令以系统管理者的身份执行指令，也就是说，经由 sudo 所执行的指令就好像是 root 亲自执行。
>
> https://www.runoob.com/linux/linux-comm-sudo.html