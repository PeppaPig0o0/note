# Requests库

```
pip install requests -i https://pypi.tuna.tsinghua.edu.cn/simple
```

## 请求

### 方法

GET POST PUT DELETE HEAD OPTIONS等方法

```python
r = requests.get(url)
r = requests.post('https://httpbin.org/post', data={'key': 'value'})
r = requests.put('https://httpbin.org/put', data={'key': 'value'})
r = requests.delete('https://httpbin.org/delete')
r = requests.head('https://httpbin.org/get')
r = requests.options('https://httpbin.org/get')
```

### url参数

```python
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.get('https://httpbin.org/get', params=payload)

# 相当于
# https://httpbin.org/get?key1=value1&key2=value2&key2=value3 
```

### post请求

```python
# 1 携带参数（字典）
payload = {'key1': 'value1', 'key2': 'value2'}
r = requests.post("https://httpbin.org/post", data=payload)

# 2 传入字符串
# 可以传入一个string而不是dict，则该数据将直接传递。
r = requests.post(url, data=json.dumps(payload))

# 3 传json字符串 （不需要自己把字典转为json，直接传字典）
payload = {'some': 'data'}
r = requests.post(url, json=payload)

# 4 传文件
files = {'file': open('report.xls', 'rb')}
r = requests.post(url, files=files)
# 可以指定文件名、内容类型、标题等
files = {'file': ('report.xls', open('report.xls', 'rb'), 'application/vnd.ms-excel', {'Expires': '0'})}
```

### 重定向和历史请求

```python
# Response.history以列表形式包含为完成请求而创建的Response对象
# 如果请求被重定向
r.history

# 可是设置是否允许重定向  allow_redirects 
r = requests.get('http://github.com/', allow_redirects=False)
```

### 超时

timeout参数

告诉请求在给定的秒数后停止等待响应。几乎所有的生产环境代码都应该在所有的请求中使用此参数。否则会导致程序无限期挂起：

```python
requests.get('https://github.com/', timeout=0.001)
# 超过timeout秒内没有发出响应，则会引发异常。
```

### Session会话

```

```









## 响应

### 响应内容

```python
# 1.响应内容
# (requests库会根据响应内容的编码进行猜测,返回猜测编码的文本)
r.text

# 2.猜测编码
r.encoding

# 3.二进制响应内容
r.content

# 4.json响应内容
r.json()
# r.json()的成功并不是表示响应的成功,有些服务器可能在失败的响应中返回一个JSON对象（例如HTTP500的错误详细信息）.若要检查请求是否成功，请使用r.raise_for_status()或r.status
```

### 响应状态码

```python
r.status_code

# 响应状态
r.raise_for_status()
# 200->None
# 如果是4XX或者5XX 则抛出异常
```

### 响应头

```
r.headers
```

### cookies

```python
r.cookies

# Cookie以RequestsCookieJar类的形式返回，它的作用类似于dict，但也提供了一个更完整的接口，适合在多个域或路径使用。


# RequestsCookieJar对象也可以传递给请求
jar = requests.cookies.RequestsCookieJar()
jar.set('tasty_cookie', 'yum', domain='httpbin.org', path='/cookies')
```

