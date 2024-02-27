# collections

![image-20231221104528949](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20231221104528949.png)

## namedtuple()

命名元组，为每个元素命名

```python
import collections

# 就好比有一个user_obj对象，里面有4个属性
userinfo = collections.namedtuple('user_obj', ['name', 'age', 'phone', 'email'])

# 分别创建3个对象
user = userinfo('admin', '18', '13578451256', '13578451256@163.com')
print(user)
print(user.name)
print(type(user))

```

![image-20231221105951255](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20231221105951255.png)

```python
# 语法
collections.namedtuple('类的名字', ['字段1', '字段2'], *, rename=False, defaults=None, module=None)
```

```python
# 实例化方式1  不能传入列表，各参数只能一个一个传入
user = userinfo('admin', '18', '13578451256', '13578451256@163.com')

# ._make()
# 实例化方式2  推荐
info = ['admin', '18', '13578451256', '13578451256@163.com']
user1 = userinfo._make(info)
```

```python
# ._asdict()
# 命名元组->字典
print(user1._asdict())
```

```python
# _replace
# 命名元组也无法修改属性，但是可以返回一个新的对象
user = user._replace(age='100')
print(user)
```

```python
# 返回所有字段名称
print(user._fields)
# 返回所有字段的名称和默认值（在声明的时候，加入了defaults参数）
print(user._field_defaults)
```

## Counter

可以理解为字典的子类，用于计算，会存储每个输入元素及该元素出现的次数。

字典存储键值对，Counter存储元素和出现次数

```python
# 实例化
c = collections.Counter('可迭代对象')
print(c)
```

![image-20231221112534602](E:\笔记\Python基础\标准库\collections.assets\image-20231221112534602.png)

```python
# 根据次数展示出所有元素（大于0次）
c = collections.Counter(a=4, b=2, c=0, d=-3)
print(list(c.elements()))
```

![image-20231221112834588](E:\笔记\Python基础\标准库\collections.assets\image-20231221112834588.png)

```python
# 返回n个出现次数最多的元素
print(c.most_common(2))
```

```python
# 从可迭代对象中减去元素计数
c = collections.Counter('AAABBCCCCCCDDDDD')
d = collections.Counter('AAAAABBCCCDD')
c.subtract(d)  # 修改原对象
print(c)
```

![image-20231221162434700](E:\笔记\Python基础\标准库\collections.assets\image-20231221162434700.png)

```python
# 元素计数增加
c = collections.Counter('AAABBCCCCCCDDDDD')
d = collections.Counter('AAAAABBCCCDD')
c.subtract(d)  # 修改原对象
print(c)
c.update({'A': 10})   # 把'A'的计数增加10
print(c)
```

![image-20231221162730192](E:\笔记\Python基础\标准库\collections.assets\image-20231221162730192.png)

```python
# 小于等于0的元素 不会被包含
print(c - d)  # 次数相减
print(c + d)  # 次数相加
print(c & d)  # 交集，最小次数
print(c | d)  # 并集，最大次数
```

```python
cou = collections.Counter(a=2, b=1, c=-2, d=-3)
print(+cou)  # 输出次数为正的元素
print(-cou)  # 输出次数为负的元素
```

## deque

双端队列，列表的扩展

允许在两端添加和删除元素

```python
# 双端队列   collections.deque('ABCDE', maxlen=最大长度)
# 允许在两端添加和删除元素
q = collections.deque('ABCDE')
q.append('F')  # 队列右端添加元素
q.appendleft('A')  # 队列左端添加元素

q.pop()  # 队列右端弹出一个元素
q.popleft()  # 队列左端弹出一个元素

print(q[0])  # 支持索引和列表相同
```

```python
# 添加多个元素
q.extend('FGH')  # 后面添加多个元素
q.extendleft('abc')  # 前面添加多个元素，注意是一个一个添加，最终效果是  倒序
```

```python
print(q)
# 旋转，n为旋转元素的个数
# 正 从右向左旋转
q.rotate(3)
print(q)
# 负 从左向右旋转
q.rotate(-3)
print(q)
```

![image-20231221165802698](E:\笔记\Python基础\标准库\collections.assets\image-20231221165802698.png)

```python
# 指定位置插入
insert(位置, 元素)
```

