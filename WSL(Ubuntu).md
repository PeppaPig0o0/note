---

---

# Ubuntu（WSL）

## 安装WSL  

第一种：打开微软商店

默认无法安装两个相同版本的wsl

第二种：

```
wsl -l --online
wsl --install -d Ubuntu
wsl --install --distribution Debian
```

## 安装两个相同版本WSL 

例 Ubuntu 20.04

### 下载：安装LxRunOffline



***弃用*** https://github.com/DDoSolitary/LxRunOffline/releases # 官方版本会出错

<img src="E:\笔记\assets\image-20231201092141844.png" alt="image-20231201092141844" style="zoom: 33%;" />

***改用这个地址***

https://github.com/Andy1208-lee/LxRunOffline.git

https://github.com/Andy1208-lee/LxRunOffline/blob/main/LxRunOffline-v3.5.0-11-gfdab71a-msvc.zip

### 解压，在目录中输入

列出当前系统中已安装的wsl系统

```
$ .\LxRunOffline.exe l
```

<img src="E:\笔记\assets\image-20231201092349779.png" alt="image-20231201092349779" style="zoom:50%;" />

```
.\LxRunOffline.exe d  -n Ubuntu-20.04 -d D:\wsl\Ubuntu-20.04-01\ -N Ubuntu-20.04-01
```



## 删除

```
# 列出子系统
wslconfig /l
或   wsl --list
```

<img src="E:\笔记\assets\image-20231201092848278.png" alt="image-20231201092848278" style="zoom: 50%;" />

```
# 删除对应系统
wslconfig /u Ubuntu-20.04-01
```

![image-20231201092947250](E:\笔记\assets\image-20231201092947250.png)

可以再输入wslconfig /l检查一下，确认注销成功

## 操作

### 列出所有wsl

```
wsl -l --all 
或 wsl --list --all
```

### 列出运行中wsl

```
wsl -l --running
```

### 列出wsl版本

```
wsl -l -v
```

### 设置默认子系统

```
wsl -s Ubuntu-20.04
```

### 查看子系统状态

```
wsl --status
```

### root身份运行

或某个其他用户

```
wsl -d Ubuntu20.04 -u root
--user, -u
```

### 关闭所有wsl

```
# 立即关闭所有wsl
wsl --shutdown
```

## pip

### 如果没有pip

```
sudo apt-get update
sudo apt-get upgrade  
```

```
sudo apt install python3-pip
```

### 全局换源

```
pip3 config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

### pip更新

```
pip3 install --upgrade pip
```

### requirements.txt

```
# 生成requirements.txt
pip freeze > requirements.txt

# 安装requirements.txt中的库
pip3 install -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple
```

