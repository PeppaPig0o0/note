# Nginx

## 简介

### 安装

nginx 1.20.1

```bash
# centos
yum -y install nginx

## 2.查看nginx版本
nginx -v
 
## 3.启动nginx
systemctl status nginx  ## 查看nginx状态
 
systemctl start nginx   ## 开启nginx服务
# 输入ip打开网页，显示下面，说明安装成功
 
systemctl stop nginx     ## 关闭nginx服务
```

![image-20231231114939474](E:\笔记\服务器\Nginx.assets\image-20231231114939474.png)

### http状态码

![image-20231231121827743](E:\笔记\服务器\Nginx.assets\image-20231231121827743.png)