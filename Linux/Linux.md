# Linux

[TOC]



www.kernel.org linux内核

分区 ：boot 分区（ext4）大小 1G；swap分区(swap)大小 2G；根分区/  (ext4)

linux 一切皆文件

## 目录结构

| /bin  /usr/bin  /usr/local/bin    | 存放常用命令                                                 |
| --------------------------------- | ------------------------------------------------------------ |
| /sbin  /usr/sbin  /usr/local/sbin | 存放系统管理员使用的系统管理程序                             |
| **/home**                         | 普通用户的主目录                                             |
| **/root**                         | 系统管理员目录                                               |
| /lib                              | 动态链接共享库，应用程序需要用到的库                         |
| /lost+found                       | 非法关机以后，目录里会有文件                                 |
| **/etc**                          | 系统配置文件                                                 |
| **/usr**                          | 用户的应用程序和文件在这个目录下，类似Windows中的Program files |
| /boot                             | 引导文件                                                     |
| /dev                              | 设备管理                                                     |
| /mnt                              | 用户挂在的文件系统                                           |
| /opt                              | 给主机额外安装软件所放的目录                                 |
| /var                              | 一般放会不断扩充的文件，类似日志                             |

## Vim

一般模式- > i或a -> 编辑模式

编辑模式- > ESC  -> 一般模式

一般模式- > :或者/ -> 命令模式（wq保存退出 w保存 q退出 q!强制退出不保存）

命令模式- > ESC   -> 一般模式



| 快捷键    | 存放常用命令            |
| --------- | ----------------------- |
| yy        | 拷贝当前行              |
| dd        | 删除当前行              |
| 4yy       | 当前行开始，拷贝4行     |
| 4dd       | 当前行开始，删除4行     |
| p         | 在当前行下面进行粘贴n行 |
| /关键词   | 查找 ，按n下一个        |
| :set nu   | 显示行号                |
| :set nonu | 不显示行号              |
| G         | 文件末行                |
| gg        | 文件首行                |
| u         | 撤销                    |
|           |                         |

## 关机、重启、登陆、注销

```
shutdown
shutdown -h now  立即关机
shutdown -h 1  
shutdown -r 1   立即重启
halt    关机
reboot 重启
sync    内存数据同步到磁盘

关机或重启前，使用sync把内存中数据写到磁盘
```

```
su -     管理员登陆 
```

## 用户管理

### 添加用户

```
useradd 用户名
# 会在自动/home下创建用户目录

useradd -d 目录 用户名
```

### 指定/修改密码

```
passwd 用户名
```

### 删除用户

```
# 删除用户，保留目录
userdel 用户名
# 删除用户，删除目录
userdel -r 用户名
```

### 查询用户信息

```
id 用户名
```

```
# 查看当前用户信息
whoami
```

## 用户组

### 新增组

```

```

## 压缩

常见的压缩格式

![image-20231206164324526](E:\笔记\Linux\assets\image-20231206164324526.png)

### gzip

```bash
# gzip应用最广泛
gzip [-cdtv#] 文档

# 解压
gzip -d 压缩包
```

![image-20231206164553646](E:\笔记\Linux\assets\image-20231206164553646.png)

### bzip2

![image-20231206164844699](E:\笔记\Linux\assets\image-20231206164844699-1701852537177-1.png)

### xz

![image-20231206164902183](E:\笔记\Linux\assets\image-20231206164902183.png)

### tar打包指令

![image-20231206165111302](E:\笔记\Linux\assets\image-20231206165111302.png)

可选参数

![image-20231206165141988](E:\笔记\Linux\assets\image-20231206165141988.png)

```shell
# 打包并压缩(通过gzip)
tar -zcvf (被压缩)目录或文件
```

```shell
# 解压
tar -zxvf 压缩包 -C 指定目录解压
```

## Bash

系统上合法的 shell 要写入 /etc/shells 这个文件， 这是因为系统某些服务在运作过程中，会去检查使用者能够使用的 shells ，而这些 shell 的查询就是藉由/etc/shells 

```
\
# 可以换行 、后面没有空格
```

![image-20231207083757353](E:\笔记\Linux\assets\image-20231207083757353.png)

```
ctrl + u  # 删除光标之前指令串
ctrl + k  # 删除光标之后指令串
```

### 变量

```bash
# 1、赋值  等号前后不能有空格
# 可以用""或''引起来
abc=mnnnn

# 2、调用
echo $abc

# 3、取消变量   没有$符号
unset abc

# 4、全大写字母默认为系统变量

# 5、指令中使用到其他变量时候，
# 可以使用$(指令)  或  `指令`
例  echo $(uname -r) 
    echo `uname -r` 
    
# 6、********
# 该变量扩增内容
# (引号或{}后面是内容，不能空格)
# 1>$PATH="$PATH":/home/bin
# 2>$PATH=${PATH}:/home/bin  推荐
例: a=qwe
a="$a":asd
a="$a"' zxc'

# 7、该变量在其他子程序执行时候，需要使这个变量变成环境变量
export
```

```bash
# 单引号和双引号
双引号可以引用变量的内容
单引号 无法保有变量的内容

```

#### env和export

查看环境变量

```
env
export
```

#### set

查看所有变量

```
set
```

## RPM、SRPM、YUM

Red Hat -> RPM

Debian -> dpkg

SRPM  SourceRPM

![image-20231207152022311](E:\笔记\Linux\assets\image-20231207152022311.png)

### RPM

#### 安装

```
rpm -i *.rpm
```

![image-20231207152549149](E:\笔记\Linux\assets\image-20231207152549149.png)

#### 升级更新

![image-20231207153044900](E:\笔记\Linux\assets\image-20231207153044900.png)

#### 查询query

![image-20231208095551988](E:\笔记\Linux\assets\image-20231208095551988.png)

#### 反安装

```
rpm -e 软件名
```

比较容易发生依赖问题无法删除

```
# 如果安装/删除/升级等导致文件破损，重建RPM数据库
rpm --rebuilddb
```

### YUM

![image-20231208101023815](E:\笔记\Linux\assets\image-20231208101023815.png)

![image-20231208101033880](E:\笔记\Linux\assets\image-20231208101033880.png)

```
yum list updates
yum list python3
yum info python
yum search python
yum list py*   # 以py开头的软件
```

#### 安装/升级

```
yum install 软件名
yum -y install 软件名       # 默认输入y

yum update 软件名
yum update                 # 升级整个系统软件
```

#### 卸载

```
yum remove 软件名
```

#### YUM换源

```
yum install -y wget
cd /etc/yum.repos.d/
mv CentOS-Base.repo CentOS-Base.repo.back
wget -O CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
yum clean all
yum makecache
```

#### 群组功能

```
yum [群组功能] [软件群组]
```

![image-20231208113404549](E:\笔记\Linux\assets\image-20231208113404549.png)