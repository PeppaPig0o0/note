Django-Scrapy
=============

1 新建Django项目
----------------

​	使用pycharm专业版安装或者pip install django==3.2.12

2 修改Django默认数据库
----------------------

在______init.py______中添加：

```
import pymysql
pymysql.install_as_MySQLdb()
```

在settings.py中修改：

```
DATABASES = {
     'default': {
         'ENGINE': 'django.db.backends.mysql', #数据库引擎
         'NAME': 'demo',                       #数据库名
         'USER': 'root',                       #用户名
         'PASSWORD': '007741ak',                   #密码
         'HOST': '120.48.34.71',                           #数据库主机，默认为localhost
         'PORT': '3306',                           #数据库端口，MySQL默认为3306
         'OPTIONS': {
             'autocommit': True,
         },
     }
 }
```

```
ALLOWED_HOSTS = ['*']
```

3.创建scrapy项目

在根目录下

```
scrapy startproject boss
```

在spiders目录下

```
scrapy genspider zhipin www.zhipin.com
```

