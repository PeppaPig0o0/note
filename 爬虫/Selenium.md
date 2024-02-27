# Selenium

## 基础

### 安装

```
pip install selenium -i https://pypi.tuna.tsinghua.edu.cn/simple
```

### 启动案例

```python
from selenium import webdriver

# 启动路径
service = webdriver.ChromeService(executable_path='./chromedriver.exe')  
# 启动选项设置
chrome_options = webdriver.ChromeOptions()  
# 实例化浏览器对象
driver = webdriver.Chrome(service=service, options=chrome_options)  
# 打开网址
driver.get('https://www.baidu.com') 

print(driver.title)  # 打印网页title
driver.quit()  # 退出


```

## 元素定位

```python
from selenium.webdriver.common.by import By
driver.find_element(By.ID, 'kw')
```

![image-20231212170031624](E:\笔记\爬虫\assets\image-20231212170031624.png)



```python
# LINK_TEXT 专门用来定位a标签文本的值，（不匹配a标签内部标签文本）
# 精确匹配a标签的文本
driver.find_element(By.LINK_TEXT, 'kw')

# PARTIAL_LINK_TEXT 模糊匹配a标签文本的值
# （！！！匹配a标签内部标签文本）
a = driver.find_elements(By.PARTIAL_LINK_TEXT, '不')
```

## 元素基本操作

```python
# 1.点击
driver.find_element(By.ID, 'kw').click()

# 2. input标签 输入 可以一个、多个，
driver.find_element(By.ID, 'kw').send_keys('mmm', 'abc'......)
# 也可以输入操作，Keys可以输入很多按键!!!!
driver.find_element(By.ID, 'kw').send_keys('mmm', Keys.ENTER)

# 3.窗口截图,png（网页很长无法截取长图，只能截取当前窗口显示出来的）
a = driver.find_element().screenshot_as_base64
a = driver.find_element().screenshot_as_png
f.write(a)

# 4.当前标签以及内部标签的文本
driver.find_element().text

# 5.当前标签宽和高
driver.find_element().size

# 6.当前标签所处页面中的位置的坐标{'x': 30, 'y': 280}
driver.find_element().location

# 7.滚动到标签，然后返回标签坐标{'x': 30, 'y': 0}
driver.find_element().location_once_scrolled_into_view

# 8.清除输入框文本
driver.find_element().clear()

# 9.**当前标签属性值
driver.find_element().get_attribute('class')
# 暂时没发现这两个有什么不同
driver.find_element().get_dom_attribute('class')
```

### 注意：

无头模式下，浏览器最大化失效，screenshot_as_png 可能会无法截取完整图片，可以使用以下方法

```python
import tkinter  # GUI工具库
tk = tkinter.Tk()  # 创建窗口
width = tk.winfo_screenwidth()  # 获取屏幕宽度
height = tk.winfo_screenheight()  # 获取屏幕高度
tk.quit()  # 关闭窗口

chrome_options.add_argument(f'--window-size={width}x{height}')
```

### WebElement元素对象

```
WebElement.tag_name  # 标签名字
WebElement.size  # 大小
WebElement.rect  # 大小和位置的字典
WebElement.parent  # 
```



## 动态执行js代码

```python
driver.execute_script("")
```

### 窗口滚动

```python
...
```

### 清除页面元素

```python
driver.execute_script("""
var a = document.getElementsByClassName("main-content container")[0];
a.parentNode.removeChild(a);
""")
```

## 元素信息

```python
# 1.检查连接的元素是否正确显示在网页上 boolean
driver.find_element(By.NAME, "email_input").is_displayed()

# 2.元素在网页上是启用还是禁用状态  boolean
.is_enabled()

# 3.确认相关的元素是否 已选定，常用于复选框、单选框、输入框和选择元素中。  boolean
is_selected()

# 4.返回与该元素的 DOM 特性或属性关联的数据
.get_attribute("value")

# 5.文本
.text
```

## 窗口

```
....
```



## swith_to

### 窗口切换

```python
# 当前窗口句柄
driver.current_window_handle

#切换窗口
driver.switch_to(driver.window_handles[-1])
driver.switch_to(driver.window_handles[0])
```

### iframe切换

```python
# 1. 切换到iframe中
a = driver.find_element(By.XPATH, '路径')
driver.switch_to.frame(a)

#  frame 或 iframe 具有 id 或 name 属性
driver.switch_to.frame('id或name')

# 2. 回到上一层iframe中
driver.switch_to.parent_frame()

# 3. 强制回到原始内容中
driver.switch_to.default_content()


driver.switch_to.alert
driver.switch_to.active_element

```

### select切换

```python
from selenium.webdriver.support.select import Select
# 1.实例化一个Select对象
sel = Select(driver.find_element(By.ID, 'id'))

# 2.选中
# 根据选项的值选择
sel.select_by_value('1')
# 根据可见选项的文本选择
sel.select_by_visible_text('1')
# 根据选项的索引选择  （最常用）
sel.select_by_index(1)

# 3.获取所有选项
sel.options  

# 4.取消选中
sel.deselect_by_index()
sel.deselect_by_value()
sel.deselect_by_visible_text()

# 5.获取第一个被选中的选项
sel.first_selected_option

# 6.获取所有被选中的选项
sel.all_selected_options

# 7.取消所有选项的选择
sel.deselect_all()

# 8.判断是否是多选
sel.is_multiple
```

### 当前激活的元素



```
a = driver.switch_to.active_element
```



## ChromeOptions

### 实例化

```python
# 第一种（建议）（两种本质上一样）
from selenium.webdriver.chrome.options import Options
chrome_options = Options()
```

```python
# 第二种
from selenium import webdriver
chrome_options = webdriver.ChromeOptions()
```

### 参数

```python
# ！！！为建议加上
# 1.忽略chrome浏览器非https警告 ！！！
chrome_options.add_argument('--ignore-certificate-errors')

# 2.无头模式 ！！！
chrome_options.add_argument('--headless') 

# 3.关闭gpu ！！！
chrome_options.add_argument('--disable-gpu')  

# 隐身模式 （还不知道作用）
chrome_options.add_argument('--incognito') 
```

eeeeeeeeee

## 等待

不要混合隐式和显式等待。这样做可能会导致不可预测的等待时间。

### 强制等待

```
time.sleep(2)
```

### 隐式等待

```python
# 在这个等待时间内，网页加载完成，则执行下一步
# 如果这个时间内，网页没有加载完成，则等待网页加载完成
# 对所有元素设置超时时间（每个请求，调用一次即可）
# 这是一个全局设置，适用于整个会话的每个元素位置调用
driver.implicitly_wait(2)
```

弊端：但有时候我需要的那个元素早就加载完成了，只是页面上有个别其他元素加载特别慢，我仍要等待页面全部加载完成才能执行下一步。

### 显式等待

仅仅等待我所需要元素，一加载出来就执行，用到显示等待

```python
# 这两个本质一样，ui.WebDriverWait 就是 wait.WebDriverWait
from selenium.webdriver.support.wait import WebDriverWait
from selenium.webdriver.support.ui import WebDriverWait
```

```python
WebDriverWait(driver=driver, timeout=10, poll_frequency=0.5, ignored_exceptions=NoSuchElementException)
"""
driver ：浏览器驱动 或 页面元素
timeout ：等待时间
poll_frequency ：检测的间隔时间，默认0.5s
ignored_exceptions ：超时后的异常信息，默认抛出NoSuchElementException
"""
```

```python
# 第一种
element = WebDriverWait(driver, 5).until(lambda x: x.find_element(by=By.ID, value=''))

# 第二种
from selenium.webdriver.support import expected_conditions as EC
element = WebDriverWait(driver, 5, 0.5).until(EC.presence_of_element_located((By.ID, "kw")))

# until_not
WebDriverWait(driver, 5 , 0.2).until_not(.......)
```

```python
# until语法 超时返回message
until(method, message)

#超时返回message
until_not(method, message)
```

## expected_conditions as EC

```python
from selenium.webdriver.support import expected_conditions as EC
```

Expected Conditions 的使用场景有2种：

- 直接在断言中使用
- 与 WebDriverWait() 配合使用，动态等待页面上元素出现或者消失

![image-20231213114209929](E:\笔记\爬虫\assets\image-20231213114209929.png)

## 元素

### 文件上传

```python
file_input = driver.find_element(By.CSS_SELECTOR, "input[type='file']")
file_input.send_keys(文件)
driver.find_element(By.ID, "file-submit").click()
```

## 交互

当前浏览器标题

```
driver.title
```

当前URL

```
driver.current_url
```

### 浏览器导航

```python
# 1.浏览器后退按钮
driver.back()

# 2.浏览器前进按钮
driver.forward()

# 3.浏览器刷新
driver.refresh()
```

### Js警告框、提示框、确认框

```python
# 警告框（只有一个确认或关闭按钮）
# 点击某个按钮 弹出警告框
driver.find_element(By.LINK_TEXT, "See an example alert").click()

# 等待警告框弹出
alert = wait.until(expected_conditions.alert_is_present())

# 获取警告框文本

text = alert.text

# 点击警告框确认按钮
alert.accept()
```

```python
# 确认框（有确认、取消两个按钮）
# 点击某个按钮 弹出确认框
driver.find_element(By.LINK_TEXT, "See an example confirm").click()

# 等待确认框弹出
alert = wait.until(expected_conditions.alert_is_present())

# 切换到确认框
alert = driver.switch_to.alert

# 获取警告框文本
text = alert.text

# Press the 取消 button
alert.dismiss()

# 点击警告框确认按钮
alert.accept()
```

```python
# 提示框（有一个输入框）
# 点击某个按钮 弹出提示框
driver.find_element(By.LINK_TEXT, "See an example confirm").click()

# 等待提示框弹出
wait.until(expected_conditions.alert_is_present())

# 实例化一个Alert对象
alert = Alert(driver)

# 输入
alert.send_keys("Selenium")

# Press the OK button
alert.accept()
```

## Actions!!!!!!



```python
# 指针移动和滚轮滚动 允许用户设置操作的持续时间, 但有时您只需要在操作之间等待一下,
    clickable = driver.find_element(By.ID, "clickable")
    ActionChains(driver)\
        .move_to_element(clickable)\
        .pause(1)\
        .click_and_hold()\
        .pause(1)\
        .send_keys("abc")\
        .perform()
```

### 鼠标

```
# 点击并保持
.click

```



### 键盘

### 滚轮

触控笔

## Cookies

```python
# 增加一个cookie
driver.add_cookie({"name": "foo", "value": "bar"})
```

```python
# 获取当前页面所有cookies（返回list）
driver.get_cookies()
```

```python
# 根据name 返回一个cookie的值
driver.get_cookie("foo")
```

```python
# 删除cookie
driver.delete_cookie("test1")
driver.delete_all_cookies()
```

![image-20231213145440046](E:\笔记\爬虫\assets\image-20231213145440046.png)



## 验证码

平台

### 超级鹰

```python
#!/usr/bin/env python
# coding:utf-8

import requests
from hashlib import md5

class Chaojiying_Client(object):

    def __init__(self, username=None, password=None, soft_id=None):
        self.username = username if username else '13299001916'
        password = password.encode('utf8') if password else '007741ak'.encode('utf8')
        self.password = md5(password).hexdigest()
        self.soft_id = soft_id if soft_id else '955947'  #用户中心>>软件ID 生成一个替换 96001
        self.base_params = {
            'user': self.username,
            'pass2': self.password,
            'softid': self.soft_id,
        }
        self.headers = {
            'Connection': 'Keep-Alive',
            'User-Agent': 'Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.1; Trident/4.0)',
        }

    def PostPic(self, im, codetype):
        """
        im: 图片字节
        codetype: 题目类型 参考 http://www.chaojiying.com/price.html
        """
        params = {
            'codetype': codetype if codetype else '1005',  #1902 验证码类型  官方网站>>价格体系 3.4+版 print 后要加()
        }
        params.update(self.base_params)
        files = {'userfile': ('ccc.jpg', im)}
        r = requests.post('http://upload.chaojiying.net/Upload/Processing.php', data=params, files=files, headers=self.headers)
        return r.json()

    def PostPic_base64(self, base64_str, codetype):
        """
        im: 图片字节
        codetype: 题目类型 参考 http://www.chaojiying.com/price.html
        """
        params = {
            'codetype': codetype,
            'file_base64':base64_str
        }
        params.update(self.base_params)
        r = requests.post('http://upload.chaojiying.net/Upload/Processing.php', data=params, headers=self.headers)
        return r.json()

    def ReportError(self, im_id):
        """
        im_id:报错题目的图片ID
        """
        params = {
            'id': im_id,
        }
        params.update(self.base_params)
        r = requests.post('http://upload.chaojiying.net/Upload/ReportError.php', data=params, headers=self.headers)
        return r.json()


if __name__ == '__main__':
    chaojiying = Chaojiying_Client()
    im = open('a.jpg', 'rb').read()													#本地图片文件路径 来替换 a.jpg 有时WIN系统须要//
    print(chaojiying.PostPic(im, 1902))


    

```

```python
# 可以吧验证码截图 然后传给超级鹰。但是图片必须肉眼可见能够截图
img = driver.find_element(By.ID, 'checkCode')
print(chaojiying.PostPic(img.screenshot_as_png, 1902))
```

注意:1.超级鹰图片识别，4位以下的验证码识别总是出错



### 图鉴



### ddddoc

#### 图片验证码

![image-20231213011915787](E:\笔记\爬虫\assets\image-20231213011915787.png)

```python
import ddddocr
ocr = ddddocr.DdddOcr(show_ad=False)        # 实例化 关闭广告
#res = ocr.classification(img.screenshot_as_png) # sel
res = ocr.classification(img.screenshot_as_png)
print(res)
```

#### 滑块验证码

```

```



## 浏览器

### 页面加载策略

```python
from selenium.webdriver.chrome.options import Options
options = Options()
options.page_load_strategy = 'normal'  # 默认值
driver = webdriver.Chrome(options=options)
```

![image-20231213142446352](E:\笔记\爬虫\assets\image-20231213142446352.png)

### 代理

```

```



