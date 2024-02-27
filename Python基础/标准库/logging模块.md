# logging模块

日志管理模块

```
import logging
```

## 五个等级

```python
logging.debug('调试日志')
logging.info('消息日志')
logging.warning('警告日志')  # 默认日志输出级别
logging.error('错误日志')
logging.critical('严重错误日志')
```

![image-20231220152853309](E:\笔记\Python基础\标准库\logging模块.assets\image-20231220152853309.png)

## 日志配置1

### logging.basicConfig

生成日志之前配置(一般不用)

```python
logging.basicConfig(
    # 日志输出级别
    # level=logging.DEBUG,
    level=10,
    # 日志输出格式（当前时间、文件名、行号、日志级别、日志信息）
    format='%(asctime)s %(filename)s[line:%(lineno)d] %(levelname)s %(message)s',
    # 日志输出时间格式
    datefmt='%Y-%m-%d %X %A',
    filename='./test.log',
    filemode='w',
    # force=True
)
```

![image-20231220164355953](E:\笔记\Python基础\标准库\logging模块.assets\image-20231220164355953.png)

### 日志配置字典（重要）

```python
# 新建日志配置文件，例如log_settings.py
LOGGING_DICT = {
    # 版本号，这个参数logging强制规定为1，否则会报错，主要是为了后续配置升级后提供兼容性
    'version': 1.0,
    'disable_existing_loggers': False,
    # 日志格式化器，每一个键值对就是一种格式化方式
    'formatters': {
        'standard': {
            'format': '[%(asctime)s] %(threadName)s:%(thread)d [%(name)s] {%(filename)s %(lineno)d} %(levelname)s - %(message)s',
            # 格式化输出格式
            'datefmt': '%Y-%m-%d %H:%M:%S'  # 日期的输出格式，即上面的asctime格式化参数
        },
        'simple': {
            'format': '[%(asctime)s] {%(pathname)s %(lineno)d} %(levelname)s - %(message)s',  # 格式化输出格式
            'datefmt': '%Y-%m-%d %H:%M:%S'  # 日期的输出格式，即上面的asctime格式化参数
        },
        'test': {
            'format': '[%(asctime)s] %(message)s',
        }
    },
    # 过滤器
    'filters': {},
    # 日志输出处理器，每一个键值对就是一个输出处理类
    'handlers': {
        'console_debug_handler': {
            'level': 'DEBUG',  # 日志处理级别
            'class': 'logging.StreamHandler',  # 输出到终端
            'formatter': 'test'  # 日志输出格式（上面formatters中定义的）
        },
        'file_debug_handler': {
            'level': 'DEBUG',  # 日志处理级别
            'class': 'logging.FileHandler',  # 输出到文件
            'formatter':'simple',  # 日志输出格式（上面formatters中定义的）
            'filename':'./debug.log',  # 日志存放文件
            'encoding': 'utf-8',
        },
        'fileHandler': {
            'class': 'logging.handlers.TimedRotatingFileHandler',  # 日志处理类
            'filename': './fileHandler.log',  # 日志存放文件
            'when': 'MIDNIGHT',  # 旋转日志（切换新的日志文件）的方式
            'interval': 1,  # 旋转的时间间隔
            'formatter': 'simple',  # 输出处理器
            'backupCount': 30 * 6,  # 备份天数,单位天
            'level': 'INFO'  # 处理器等级
        }
    },
    # 日志记录器，每一个键值对就是一个记录器。loggers负责生产不同级别的日志，handlers负责处理日志，filters负责过滤日志
    'loggers': {
        'logger1': {
            'handlers': ['console_debug_handler'],  # 产生的日志丢给哪些handler处理
            'level': 'DEBUG',  # 日志等级
            'propagate': False  # 默认为True，向上级（更高级别的logger）传递日志
        },
        'logger2': {
            'handlers': ['console_debug_handler', 'file_debug_handler'],  # 产生的日志丢给哪些handler处理
            'level': 'INFO',  # 日志等级
            'propagate': False  # 默认为True，向上级（更高级别的logger）传递日志
        }
    }
}
```

使用时候

```python
import logging
import logging.config
import log_setting

# 使用dictConfig引入日志配置字典
logging.config.dictConfig(log_setting.LOGGING_DICT)

logger2 = logging.getLogger('logger2')
logger2.debug(f'{logger2.name} debug日志')
logger2.info(f'{logger2.name} info日志')
logger2.warning(f'{logger2.name} warning日志')
# 可以通过getLogger实例多个logger
```

## 日志轮转

如果日志文件过大，打开日志会很卡

```python
# 在日志配置字典中
'file_info_handler': {
            'level': 'DEBUG',  # 日志处理级别
            'class': 'logging.handlers.RotatingFileHandler',  # 输出到文件
            'maxBytes': 1024*1024*10,  # 日志大小10M
			'backupCount': 10, # 日志文件保存数量限制
            'formatter':'standard',  # 日志输出格式（上面formatters中定义的）
            'filename':'./debug.log',  # 日志存放文件，尽量不要在其他handler中使用
            'encoding': 'utf-8',
        },
```

注意：被日志轮转的文件，不能和其他handler公用文件，否则多个handler打开文件，日志轮转时无法重命名。

```python
# 可以这样写
'file_info_handler': {
            'level': 'INFO',  # 日志处理级别
            'class': 'logging.handlers.TimedRotatingFileHandler',  # 轮转输出到文件
            'formatter':'standard',  # 日志输出格式（上面formatters中定义的）
            'filename': './abc.log',  # 日志存放文件
            'encoding': 'utf-8',
            'maxBytes': 100*1024*1024,  # 日志文件大小，单位字节
            'backupCount': 999999999,  # 日志文件备份数量
        },
```

## 日志配置2

日志配置文件模式，配置文件一般有ini、conf、cfg的文件格式

新建log.ini或log.cfg

```ini
# 定义1ogger
[loggers]
#创建三个app名,root是父类，必需存在的(和配置字典里面的空名字logger一样，在getLogger的时候，找不到对应的1ogger名字的，则使用root这个1ogger)
keys=root,error,info

# 定义handler
[handlers]
#定义过滤器名称(keysname)，下面定义以handler_keysname格式定义，引用的时候用keysname
keys=console,file,rotating_file

# 定义formatter
[formatters]
#定义格式名称 (keysname) ,下面定义以formatter_keysname格式定义，引用的时候用keysname
keys=standard,simple


# loggers
[logger_root]
#创建完的app名我们要定义一些规则,严格要求格式为"logger_appname'
#设置日志级别
level=DEBUG
#在"root"下的appname可填可不填
qualname=root
#设置指定过滤器，多个以逗号分隔
handlers=console,file

[logger_error]
level=ERROR
#除了root下的appname以外，所有的app必须要设置这个属性，用于定义打印输出时候的app名
qualname=error
handlers=console
# 禁止日志向父类传递
propagate=0

[logger_info]
level=INFO
qualname=info
handlers=file,rotating_file
propagate=0


# handles
[handler_console]
class=StreamHandler
level=DEBUG
formatter=simple

[handler_file]
class=FileHandler
level=DEBUG
formatter=standard
#创建文件名字，以什么方式打开，并指定编码方式
args=('./info.log', 'a', 'utf-8')

[handler_rotating_file]
level=DEBUG
# 日志轮转
class=handlers.RotatingFileHandler
formatter=standard
args=('user.log', 'a')
#日志大小10M,文件保存数量2，编码方式utf-8
kwargs={'maxBytes': 1024*1024*10, 'backupCount': 1000, 'encoding': 'utf-8'}


# formatters
[formatter_standard]
format=%(asctime)s %(threadName)s:%(thread)d [%(name)s] %(levelname)s [%(pathname)s:%(lineno)d] %(message)s
datefmt=%Y-%m-%d %H:%M:%S

[formatter_simple]
format=%(asctime)s [%(name)s] %(levelname)s %(message)s
datefmt=%Y-%m-%d %H:%M:%S

```

```ini
# 注:注释必须单独写在一行，不能跟在配置项后面，
# 我这里只是为了方便注释说明,你用的时候需要把注释去掉，或者改到单独一行去。
```

```python
# 使用dictConfig引入日志配置字典
import logging.config

# 不再用dictConfig
# logging.config.dictConfig(log_setting.LOGGING_DICT)

# 和日志配置字典用法一样，只是这一步使用fileConfig来加载篇日志文件就好了
logging.config.fileConfile('log.conf')

logger = logging.getLogger('info')
logger.info('这是Info日志')
```



问题：（在Mac和Linux上不会出现）使用fileConfig读取log.conf时，无法设置字符编码，如果你的文件是utf-8编码，且在windows系统中运就报错编码错误。

![image-20231221100151702](E:\笔记\Python基础\标准库\logging模块.assets\image-20231221100151702.png)

解决方法 1:

需要改源码（把read函数的encoding参数默认的None，改成utf-8）：

fileConfig() -> cp.read(fname) -> def read(self, filenames， encoding='utf-8'):

只有windows系统需要改此源码，其他系统不用改，或者你的配置文件本身就是gbk编码的，也不用改此选项总之改源码不太好，所以我们一般用配置字典，比较少用配置文件



解决办法2 ：

python 3.10开始,加入了encoding

```python
logging.config.fileConfig('./log.ini', encoding='utf-8')
```

