# time标准库

```python
import time
```

## 时间戳

```python
# 小数点前10位，小数点后7位
time.time()
```

## 格式化时间-字符串

主要用于展示时间

### time.strftime

```python
# 语法：                   
# time.strftime('时间格式', [结构化时间])					不写结构化时间默认当前时间

# 23年12月20日
f1 = time.strftime('%Y-%m-%d %H:%M:%S')
print(f1)  # 2023-12-20 12:00:25

f2 = time.strftime('%Y-%m-%d %H:%M:%S %A')
print(f2)  # 2023-12-20 12:00:25 Wednesday

f3 = time.strftime('%Y-%m-%d %X %A')
print(f3)  # 2023-12-20 12:00:25 Wednesday

f4 = time.strftime('%x %X %A')
print(f4)  # 12/20/23 12:02:10 Wednesday   
```

```
%x 默认格式  %m/%d/%y
%X 默认格式  %H:%M:%S
```

![image-20231220120454247](E:\笔记\Python基础\标准库\time标准库.assets\image-20231220120454247.png)

## 结构化时间

主要用于计算

```python
res = time.localtime()
print(res)

print(res.tm_year)
print(res.tm_mday)
```

![image-20231220121035730](E:\笔记\Python基础\标准库\time标准库.assets\image-20231220121035730.png)



## datetime模块

```python
res = datetime.datetime.now()
print(res)  # 打印格式化时间
# 2023-12-20 12:17:34.329836  

print(res.replace(year=2020))  # 把年替换
# 2020-12-20 12:17:34.329836

print(res.replace(month=1))  # 把月替换
# 2023-01-20 12:17:34.329836

print(res.replace(microsecond=0))  # 去掉微秒
# 2023-12-20 12:17:34
```



datetime模块可以直接做时间的加减运算

```python
# datetime.timedelta() 
# 参数 days, seconds, microseconds, milliseconds, minutes, hours, weeks

# 时间加一周
res = datetime.datetime.now() + datetime.timedelta(weeks=1)
print(res)
```

```python
datetime.datetime.now()
datetime.datetime.utcnow()  # 格林尼治时间

# 时间戳直接
t1 = 1111111111
t2 = 111111111
print(datetime.datetime.fromtimestamp(t1))
print(type(datetime.datetime.fromtimestamp(t1)))
print(datetime.datetime.fromtimestamp(t1) - datetime.datetime.fromtimestamp(t2))
```

![image-20231220145509383](E:\笔记\Python基础\标准库\time标准库.assets\image-20231220145509383.png)



## 转换

1. 时间戳 ---------->结构化时间 ---------->格式化的时间字符串

```python
# 时间戳--->结构化时间
res = time.time()
print(time.localtime(res))  # 当地时间
print(time.gmtime(res))  # 格林尼治时间
```

![image-20231220142304605](E:\笔记\Python基础\标准库\time标准库.assets\image-20231220142304605.png)

```python
# 结构化时间--->格式化时间字符串
res = time.time()
struct_time = time.localtime(res)
print(time.strftime('%Y-%m-%d %H:%M:%S', struct_time))
```

![image-20231220142603308](E:\笔记\Python基础\标准库\time标准库.assets\image-20231220142603308.png)

2.时间戳 <----------结构化时间 <----------格式化的时间字符串

```python
# 格式化时间字符串-->结构化时间
t = '2022-12-3 6:11:59'
print(time.strptime(t, '%Y-%m-%d %H:%M:%S'))
t = '2022-12-3 6:11:59 Wednesday'
print(time.strptime(t, '%Y-%m-%d %H:%M:%S %A'))
t = '2022-12-3 6:11:59 Wednesday'
print(time.strptime(t, '%Y-%m-%d %X %A'))
```

```python
# 结构化时间-->时间戳
t = '2022-12-3 6:11:59 Wednesday'
res = time.strptime(t, '%Y-%m-%d %X %A')
print(time.mktime(res))
```

## 其他方法

```python
time.asctime()  # 固定格式
time.ctime(time.time())  # 固定格式


```

