# Pandas

## Series

### 创建Series

```python
# 1、列表创建
obj_a = Series([1,2,3,4,5])
# 2、指定索引的名字
obj_a = Series([1,2,3,4,5], index=['a','b','c','d','e'])
# 3、字典创建
dic = {'a':1,'b':2,'c':3}
obj_a = Series(dic, index=['a','b','c'])
```

### Series对象访问

```python
# 一个值
obj_a['b']
# 多个值
obj_a[['a', 'c']]
```

```python
# 赋值
obj_a['b'] = 999
obj_a['b']
```

### Series对象属性

```python
obj_a.index  # 索引
```

<img src="E:\笔记\数据分析\Pandas.assets\image-20240226162534325.png" alt="image-20240226162534325" style="zoom:50%;" />

```python
obj_a['d'] = 999
obj_a.value_counts()  # 值的数量
```

<img src="E:\笔记\数据分析\Pandas.assets\image-20240226162638049.png" alt="image-20240226162638049" style="zoom:50%;" />

```python
obj_a.values  # 打印Series
obj_a.first_valid_index()  # 第一个索引
obj_a.last_valid_index()  # 最后一个索引
```

## Dataframe

### 创建

```python
# 1.字典创建
dic = {'a':[1,2,3],'b':[4,5,6],'c':[7,8,9]}
df1 = DataFrame(dic)
```

```python
# 2.指定列索引/行索引
df1 = DataFrame(dic, columns=['b','a','d'])  # 值不存在为NaN
df1 = DataFrame(dic, index=['b','a','d'])
```

```python
# 3.转置行和列
DataFrame.from_dict(dic, orient='index')
```

### 属性

```python
df.columns  # 列索引
df.index  # 行索引
df.values  # 值，不展示索引
```

### 访问

```python
# 定位行
df2.loc[1]  # 行索引
# 定位一个值
df2.loc[1, 'A']  # 行索引+列索引
```

```python
# 定位列
df2['A'] # 列索引
# 定位一个值
df2['A'][1] # 列索引+行索引
```

```python
# iloc
df2.iloc[2, 1]  # 第2行 第1列。从0开始
df2.iloc[1:3]  # 第1行到第2行。从0开始，末尾选不到
```

