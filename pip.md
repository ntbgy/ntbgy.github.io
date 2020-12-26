pip

## 安装

sudo easy_install pip

## 列出已安装的包

```
pip freeze` or `pip list
```

### 导出requirements.txt

```
pip freeze > <目录>/requirements.txt
```

## 安装包

### 在线安装

```
pip install <包名>` 或 `pip install -r requirements.txt
```

*通过使用== >= <= > <来指定版本，不写则安装最新版*

requirements.txt内容格式为：

```
APScheduler==2.1.2
Django==1.5.4
MySQL-Connector-Python==2.0.1
MySQL-python==1.2.3
PIL==1.1.7
South==1.0.2
django-grappelli==2.6.3
django-pagination==1.0.7
```

### 安装本地安装包

```
pip install <目录>/<文件名>` 或 `pip install --use-wheel --no-index --find-links=wheelhouse/ <包名>
```

*<包名>前有空格*

可简写为

```
pip install --no-index -f=<目录>/ <包名>
```

### 卸载包

```
pip uninstall <包名>` 或 `pip uninstall -r requirements.txt
```

### 升级包

```
pip install -U <包名>
```

或：pip install <包名> --upgrade

### 升级pip

```
pip install -U pip
```

## 显示包所在的目录

```
pip show -f <包名>
```

## 搜索包

```
pip search <搜索关键字>
```

## 查询可升级的包

```
pip list -o
```

## 下载包而不安装

```
pip install <包名> -d <目录>` 或 `pip install -d <目录> -r requirements.txt
```

## 打包

```
pip wheel <包名>
```

```
更换国内pypi镜像
国内pypi镜像
阿里：https://mirrors.aliyun.com/pypi/simple
豆瓣：http://pypi.douban.com/simple
中国科学技术大学：http://pypi.mirrors.ustc.edu.cn/simple/
```

### 指定单次安装源

```
pip install <包名> -i https://mirrors.aliyun.com/pypi/simple
```

### 指定全局安装源

在unix和macos，配置文件为：$HOME/.pip/pip.conf
在windows上，配置文件为：%HOME%\pip\pip.ini

```
[global]
timeout = 6000
index-url = http://pypi.douban.com/simple
```