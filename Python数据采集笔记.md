# Python数据采集笔记

## 安装和引入库

库：别人写好的代码，能拿过来直接用。标准库和第三方库

标准库：不用安装；第三方库：还要安装

1.安装第三方库（在终端里输入）

```shell
pip install 库名字 -i https://pypi.tuna.tsinghua.edu.cn/simple
# 例 requests库安装
pip install requests -i https://pypi.tuna.tsinghua.edu.cn/simple
```

2.引入库（第一种）

```python
# 把整个库引入到当前文件，独立的命名空间
语法：import 库名字
# 例
import time
使用：time.sleep(5)
```

3.引入库（第二种）

```python
# 引入库中的某一个模块或方法，当前命名空间
语法：from 库名字 import 模块名字\方法名字
# 例
from lxml import etree
from time import sleep
使用：etree......
使用：sleep(5)
```

4.建议：先引入标准库，再引入第三方库，最后引入自己写的包\模块

## 新建目录（文件夹）

- 第一种

```python
# 需要用到os标准库
import os
def make_dir(path):
    if os.path.exists(path):  # 判断路径是否存在，如果路径存在
        return '目录已存在'
    os.mkdir(path)  # 根据路径新建目录
    return '已新建目录！'
```

- 第二种

```python
# 需要用到os标准库
import os
def make_dir(path):
    try:
        os.mkdir(path)  # 正常情况，执行创建目录操作
        return '已新建目录！'
    except FileExistsError:  # 如果报错（目录存在错误）
        return '目录已存在！'
```

## 读取\写入文件

模式：

- w 写入模式（覆盖写入），r 读取模式，a 读写模式，
- “+” 追加模式，w+ 模式（追加写入），a+ 追加模式（常用），
- wb 以二进制形式写入，rb 以二进制形式读取。常用于图片或视频文件

### with open

读\写完成会自动关闭文件

```python
with open('文件路径', '模式', encoding='utf-8') as f:
	f.read()  # 读取整个文件内容
    f.readline()  # 每次读取一行内容
    f.readlines()  # 读取整个文件，返回列表（每行文本）
    f.write(文本内容)  # 向文件中写入内容
```

### open

需要手动关闭文件

```python
f = open('文件路径', '模式', encoding='utf-8')
f.read()  # 读取整个文件内容
f.readline()  # 每次读取一行内容
f.readlines()  # 读取整个文件，返回列表（每行文本）
f.write(文本内容)  # 向文件中写入内容
f.close()  # 关闭文件
```

## 延迟

适当位置加入延迟，能提高爬虫的稳定性和隐蔽性，但是会降低爬虫的效率

```python
import time
# 语法：time.sleep(数字)     延迟××秒
time.sleep(2.8)
```

## random(标准库)

有些网站会进行用户行为分析。爬虫的请求时间、顺序等非常具有规律性，可能会被识别为爬虫。

频率：有些网站可能针对同一个用户、同一个IP地址,短时间内的访问频率进行限制，如果超过这个限制，可能会触发某种机制(验证码、弹窗让你等一等、让你重新登录)

### random

```python
# ① random.random() 生成0-1之间随机浮点数
a = random.random()
print(f'a是{a}')
```

### randint

```python
# ② random.randint(小整数, 大整数)
# 从两个整数之间生成一个随机整数。可以取到最小值和最大值
b = random.randint(51, 187)
print(f'b是{b}')
```

### uniform

```python
# ③ random.uniform(整数\浮点数, 整数\浮点数)
# 从两个数字之间生成一个随机浮点数。
c = random.uniform(0.5, 2)
print(f'c是{c}')
```

### randrange

```python
# ④ random.randrange(0, 100, 10)
# 从指定最大最小值之间，根据步长进行随机。最大值取不到
d = random.randrange(0, 100, 10)
print(f'd是{d}')
```

### choice和choices

```python
# ⑤ random.choice() 从字符串或列表中随机选一个字符
# random.choices() 从字符串或列表中随机选k个字符
e = random.choice('22高职计算机8班')
print(f'e是{e}')
f = random.choice(['任移旺', '郑大川', '孙玉松', '丁旭'])
print(f'f是{f}')
g = random.choices(['任移旺', '郑大川', '孙玉松', '丁旭'], k=2)
print(f'g是{g}')
```

### shuffle   打乱

```python
# ⑥ random.shuffle() 打乱列表或字符串，修改原对象，返回
h = ['hehe', 'xixi', 'heihei', 'haha', 'zhizhi']
random.shuffle(h)
print(h)
m = [1, 2, 3, 4, 5]
random.shuffle(m)
print(m)
```

### sample  取样

```python
# ⑦ random.sample(列表, 长度)  根据长度从列表、字符串中随机切片
list1=[1,2,3,4,5,6,7,8,9,10]
slice=random.sample(list1,5)
print(slice)
x = random.sample(range(0, 10), 5)
print(x)
Words = "AppleKMedoide"
print(random.sample(Words, 3))
print(random.sample(Words, 3))
```

## 主函数

```python
# 函数 function
def fun1():
    print(f'这是fun1函数')
def fun2():
    print(f'这是fun2函数')
def fun3():
    print(f'这是fun3函数')

# 以主函数运行,py文件开始运行时会先运行主函数
# 别的文件引入此文件时，主函数内容不会执行
if __name__ == '__main__':
    print(f'这是主函数')
    fun1()
    fun2()
    fun3()
```



## 多线程

一个进程中可以同时运行多个线程，这些线程共享资源和空间

```python
# threading 是标准库
import threading
# 语法
threading.Thread(target=目标函数, name=线程名, args=(参数1,), kwargs=字典参数)
```

案例1：

```python
"""
多线程：threading
一个进程中可以同时运行多个线程，这些线程共享资源和空间
（GIL：全局解释器锁。因为GIL存在，所以一个进程中，同一个时刻只有一个线程在执行）
(所以python的多线程是假的)
"""
import threading
import time
def 杨晴():
    for i in range(1, 51):
        print(f'杨晴：这是{i}')
        time.sleep(0.1)
def 杨亚楠():
    for i in range(51, 101):
        print(f'杨亚楠：这是{i}')
        time.sleep(0.25)
if __name__ == '__main__':
    print('这是主线程/函数')
    # threading.Thread(target=目标函数, name=线程名, args=列表参数, kwargs=字典参数)
    t1 = threading.Thread(target=杨晴)  # 定义线程1
    t2 = threading.Thread(target=杨亚楠)  # 定义线程2
    t1.start()  # 线程1 开始运行
    t2.start()  # 线程2 开始运行
```

![image-20231023092509660](E:\笔记\Python数据采集笔记.assets\image-20231023092509660.png)



案例2：

```python
"""
案例2：赛跑
三位同学赛跑100米，速度不同（10，5，8）
看那位同学先跑到终点
"""
import time
import threading
def run(v):
    for i in range(50):
        # 打印线程名字 threading.current_thread().name
        print(f'{threading.current_thread().name} 跑到第{i}米')
        time.sleep(1/v)
    print(f'{threading.current_thread().name} 跑完啦！')

if __name__ == '__main__':
    # target目标函数 ， name 线程名字，  args参数
    t1 = threading.Thread(target=run, name='杨晴', args=(10, ))
    t2 = threading.Thread(target=run, name='孔灿湘', args=(5,))
    t3 = threading.Thread(target=run, name='李明君', args=(8,))
    t1.start()
    t2.start()
    t3.start()
```

![image-20231023094459986](E:\笔记\Python数据采集笔记.assets\image-20231023094459986.png)

## Selenium

selenium第三方库，自动化工具，软件测试

### 设置路径

```python
from selenium import webdriver
service = webdriver.Chromedriver(executable_path='chromedriver.exe')
```

### 实例化浏览器窗口

打开一个浏览器窗口(空白)

```python
browser = webdriver.Chrome(service=service)
```

### 访问URL

```python
browser.get(url)
```

### 关闭浏览器

```python
browser.close()
```

### 元素定位

```python
from selenium.webdriver.common.by import By
# 定位一个元素 -> 返回一个元素
browser.find_element(by=By.方式, value=值)
browser.find_element(By.方式, 值)
```

```python
# 定位多个元素 - 返回一个列表[多个元素]
browser.find_elements(by=By.方式, value=值)
browser.find_elements(By.方式, 值)
```

```python
# 九种定位

# 1.根据 元素id的值，定位元素位置
browser.find_element(By.ID, 'id的值')
# 2.根据 元素class的值，定位元素位置
browser.find_element(By.CLASS_NAME, 'class的值')
# 3.根据 元素name的值，定位元素位置
browser.find_element(By.NAME, 'name的值')
# 4.根据 元素标签名字，定位元素位置
browser.find_element(By.TAG_NAME, '标签名字')
# 5.根据 Xpath路径，定位元素位置
browser.find_element(By.XPATH, 'Xpath路径')
# 6.根据 CSS选择器，定位元素位置
browser.find_element(By.CSS_SELECTOR, 'CSS选择器')
```









### 启动路径

```python
service = webdriver.ChromeService(executable_path='chromedriver.exe')
```

### 实例化浏览器对象

打开一个浏览器窗口

```python
browser = webdriver.Chrome(service=service)
```

### 打开网址

```python
browser.get(url)
```

### 关闭浏览器

```python
browser.close()
```

### 元素定位

```python
from selenium.webdriver.common.by import By
# 定位一个元素 -> 返回一个元素
browser.find_element(by=By.方式, value=值)
browser.find_element(By.方式, 值)
```

```python
# 定位多个元素 -> 返回一个列表
browser.find_elements(by=By.方式, value=值)
browser.find_elements(By.方式, 值)
```

```python
# 9种元素定位方式 语法：

# 1.根据标签的 id属性 定位元素
browser.find_element(By.ID, 'id值')
# 2.根据标签的 class属性 定位元素
browser.find_element(By.CLASS_NAME, 'class的值')
# 3.根据标签的 name属性 定位元素
browser.find_element(By.NAME, 'name值')
# 4.根据 标签的名字 定位元素
browser.find_element(By.TAG_NAME, '标签的名字')
# 5.根据 Xpath路径 定位元素
browser.find_element(By.XPATH, 'Xpath路径')
# 6.根据 CSS选择器 定位元素
browser.find_element(By.CSS_SELECTOR, 'CSS选择器')
```

### 简单动作和取值

```python
a = browser.find_element(By.ID, '值')

# 1.输入->input
a.send_keys('王者荣耀')

# 2.左键点击
a.click()

# 3.取文本内容
a.text

# 4.取属性值
a.get_attribute('属性名')
a.get_attribute('href')
a.get_attribute('src')
```

### 窗口滚动

```python
# 窗口滚动1
browser.execute_script('window.scrollTo(左右滚动值, 上下滚动值)')
```

```python
# 窗口滚动2
def scroll_to_position(y=0, x=0):
    for i in range(1, 11):
        browser.execute_script(f'window.scrollTo({(x/10)*i}, {(y/10)*i})')
        time.sleep(0.4)
        
scroll_to_position(3000)
```

### 窗口大小

```python
# 改变浏览器窗口大小
browser.maximize_window()  # 最大化窗口
browser.minimize_window()  # 最小化窗口
browser.fullscreen_window()  # 全屏显示窗口
# 根据尺寸设置窗口大小
browser.set_window_size(width=800, height=1000)
```

