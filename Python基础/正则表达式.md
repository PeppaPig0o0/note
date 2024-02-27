# 正则表达式

## 匹配次数

、*   ？  +

![image-20231219233342209](E:\笔记\Python基础\assets\image-20231219233342209.png)

{  }

```
{5}    精确匹配5次
{0,5}  匹配0-5次
{2,}   匹配2次及以上
{,5}   错误！没有这种语法
```

## 多个字符

(  ) 用小括号括起来

```
(xy)+   表示xy匹配1次或1次以上
```

## 或

（|）  竖线

```
我叫张三
我叫李四
正则： 我叫(张三|李四)          # 必须要小括号，不然就变成了：我叫张三|李四
我叫王五
正则： 我叫(张三|李四|王五)      # 可以多个竖线
```

## 字符类

[  ]   中括号：表示匹配的字符只能限定在中括号内的字符

```
[xyz]+   	表示匹配字符1次或多次，但字符只能是xyz当中的
[a-z]    	小写字母
[A-Z]    	大写字母
[a-zA-Z] 	英文字母
[0-9]		数字
[a-zA-Z0-9] 英文字母和数字
```

[^]   表示非！！

```
[^a-z]    	非小写字母
[^0-9]    	非数字
以此类推
```

## 元字符

![image-20231219235211428](E:\笔记\Python基础\assets\image-20231219235211428.png)

```
\w  ==  [a-zA-Z0-9]    英文字母和数字
\W  ==  [^a-zA-Z0-9]   英文字母和数字
\d  ==  [0-9]   		数字
\D  ==  [^0-9]   	   非数字
```

## 贪婪和懒惰匹配

默认贪婪匹配

?会把贪婪匹配替换为懒惰匹配

```python
# 1. 例
'<html><span>哈哈哈哈哈哈123789嘿嘿</span></html>'
# 正则 <.+>   会匹配整个字符串
# 正则 <.+?>   会匹配4个标签 <......>

# 2.
```

手机号

```
13566668888
正则：
^(13[0-9]|14[5-9]|15[0-35-9]|166|17[2-35-8]|18[0-9]|19[02-89])\d{8}$

13566668888 13566668899
或
\b(13[0-9]|14[5-9]|15[0-35-9]|166|17[2-35-8]|18[0-9]|19[02-89])\d{8}\b

懒人办法
\b1\d{10}\b
```

## re.findall

```python
re.findall('正则表达式', '目标字符串', [flags])
```

```python
# ！！注意！！
res = re.findall(r'\b(13[0-9]|14[5-9]|15[0-35-9]|166|17[2-35-8]|18\d|19[02-89])\d{8}\b', s)

# 注意1： ！这样只能匹配到前三位！
# 因为python里小括号的原因，只匹配了小括号内的内容
# 解决方法，在左小括号后面加上?:   声明小括号不是一个group
res = re.findall(r'\b(?:13[0-9]|14[5-9]|15[0-35-9]|166|17[2-35-8]|18\d|19[02-89])\d{8}\b', s)

# 注意2: 因为转义字符的存在， 所以一般正则表达式前面加上 r 
```

## re.search

```python
# 只返回匹配的第一个， 返回match对象。匹配不到返回None
# 例子
res = re.search(r'\d{3,4}-\d{7,8}', 'Tel:028-7654321')
print(res)
```

![image-20231220004633857](E:\笔记\Python基础\assets\image-20231220004633857.png)

```python
res.span()  # 返回索引
```

![image-20231220004902242](E:\笔记\Python基础\assets\image-20231220004902242.png)

```python
res.group()  # 返回结果
```

![image-20231220004943681](E:\笔记\Python基础\assets\image-20231220004943681.png)

```python
# 注意
res = re.search(r'(\d{3,4})-(\d{7,8})', 'Tel:028-7654321')
print(res)
print(res.span())
print(res.group())  # 028-7654321
print(res.group(0))  # 028-7654321
print(res.group(1))  # 028
print(res.group(2))  # 7654321
# print(res.group(3))  # 没有3 会报错
print(res.groups())  # ('028', '7654321')
```

![image-20231220005157046](E:\笔记\Python基础\assets\image-20231220005157046.png)

## re.finditer

```python
res = re.finditer(r'(\d{3,4})-(\d{7,8})', 'Tel:028-7654321 Tel:029-88885555')
print(res)
# 返回一个迭代器对象
```

```python
for i in res:
    print(i) # res里面都是match对象
```

![image-20231220005555771](E:\笔记\Python基础\assets\image-20231220005555771.png)



```python
# 把网页中的请求头参数，复制过来，根据正则表达式，改为字典
# chrome浏览器，记得开启原始模式
request_headers = '''
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7
Accept-Encoding: gzip, deflate
Accept-Language: zh-CN,zh;q=0.9,en-US;q=0.8,en;q=0.7
Cache-Control: no-cache
Connection: keep-alive
Cookie: Hm_lvt_74e11efe27096f6ef1745cd53f168168=1702954797; blk=0; Hm_lpvt_74e11efe27096f6ef1745cd53f168168=1702973435
Host: www.htqyy.com
Pragma: no-cache
Referer: https://www.baidu.com/link?url=LGk40TPUE1XghjuuJb983d8Dk1SziHORIcbxSqCuM1q&wd=&eqid=cd2fc39c0060987a0000000365814ff0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36
'''

res = re.finditer(r'(.*): (.*)', request_headers)
print({i.group(1): i.group(2) for i in res})

```

## re.match

```python
# 不常用
# match返回match对象，但是match只能匹配字符串的开头（不是每行开头）
# 匹配不到返回None

s = 'ccabc5646abc'
res = re.match(r'ab', s)
print(res)
```

## re.fullmatch

```python
# 不常用
# 从头匹配到尾
# 需要完整字符串，都要符合fullmatch的规则
s = 'ccabc5646abc'
res = re.fullmatch(r'\w+ab\w+', s)
print(res)
```

## re.sub

替换

```python
# 语法
# re.sub('正则', '替换的新字符串', '原字符串')
res = re.sub(r'(\d{3,4})-(\d{7,8})', '***', 'Tel:028-7654321 Tel:029-88885555')
print(res)
```

![image-20231220011255417](E:\笔记\Python基础\assets\image-20231220011255417.png)

```python
# 中间参数也可以传入函数
# 例：  电话号码脱敏
def des(match):
    tel = match.group(2)
    return tel[:2] + '***' + tel[-2:]

res = re.sub(r'(\d{3,4})-(\d{7,8})', des, 'Tel:028-7654321 Tel:029-88885555')
print(res)
```

![image-20231220011646561](E:\笔记\Python基础\assets\image-20231220011646561.png)

```python
# 中间参数也可以传入函数
# 例：  手机号码脱敏
s = '''13566226688
1329988555432
bv13255874112
18933251125 13299001916
19246478533
14622224444'''
def des(match):
    tel = match.group()
    return tel[:3] + '****' + tel[-4:]

res = re.sub(r'\b(?:13[0-9]|14[5-9]|15[0-35-9]|166|17[2-35-8]|18\d|19[02-89])\d{8}\b', des, s)
print(res)

# 中间匹配不到的字符串 不换
```

![image-20231220012057225](E:\笔记\Python基础\assets\image-20231220012057225.png)

## re.subn

```python
# 语法
# re.subn('正则', '替换的新字符串', '原字符串', [匹配次数] )
# 匹配次数不写默认都匹配
res = re.subn(r'(\d{3,4})-(\d{7,8})', '***', 'Tel:028-7654321 Tel:029-88885555')
print(res)

# 返回时，会返回匹配次数
```

## re.split

```python
# 语法
# re.split('正则', 字符串, [次数])
# 把正则匹配到的字符串作为分隔符
s = 'nihao -. haha !heihei   hello'
res = re.split('\W+', s)
print(res)
```

![image-20231220012715269](E:\笔记\Python基础\assets\image-20231220012715269.png)

## re.compile

```python
# 把正则表达式存起来,方便后续重复使用

s = 'nihao -. haha !heihei   hello'

res = re.compile('\W+')
r = re.split(res, s)
print(res)
```

![image-20231220012914797](E:\笔记\Python基础\assets\image-20231220012914797.png)

## flags

前面所有方法，都可以用flag

```python
# 忽略大小写 IGNORECASE 
re.I  

# 多行匹配  MULTILINE  
re.M 

# 让 点. 匹配所有字符(包括\n)   DOTALL       
re.S  
```

