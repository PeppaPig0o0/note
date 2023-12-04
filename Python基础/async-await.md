# async/await

## 事件循环

理解成一个死循环，去检测并执行某些代码。

```python
import asyncio

# 去生成或获取一个事件循环
loop = asyncio.get_event_loop()

# 将任务放到“任务列表”中
loop.run_until_complete(任务)
```

## 定义 async

协程函数，定义函数时候 前面加上 async

协程对象，执行 携程函数，返回协程对象

注意：执行协程函数创建协程对象，协程函数内部的代码不会执行。

如果想要运行协程函数的代码，必须要把协程对象交给事件循环来处理。

```python
# python 3.7 之前写法
async def func():
    print(123)

result = func()
loop = asyncio.get_event_loop()
loop.run_until_complete(result)
```

```python
# python 3.7 以后
# 之前写法
async def func():
    print(123)

result = func()
asyncio.run(result)
```

## await

await 后面跟 可等待对象 （协程对象、Future、Task对象 ->IO等待）

当事件循环遇到await 关键字后，会调度其他任务



```python
async def rand_int():
    return random.randint(2, 8)


async def func():
    print('开始')
    r1 = await rand_int()
    print(r1)
    r2 = await rand_int()
    print(r2)

asyncio.run(func())
```

## Task对象

在事件循环中添加多个任务

![image-20231024163741600](E:\笔记\async-await.assets\image-20231024163741600.png)

```

```

## future对象

Task的基类

## uvloop

是asyncio事件循环替代方案，效率提高

```
pip install uvloop
```

```
import
```





## 异步类

第一种 

```python
class Z:

    async def __new__(cls, *args, **kwargs):
        instance = super().__new__(cls)
        await instance.__init__(*args, **kwargs)
        return instance

    async def __init__(self, n):
        await self.__run()

    async def __run(self):
    	pass
    	
async def main():
    tasks = [asyncio.create_task(Z(i)) for i in range(1, 5)]
    res = await asyncio.gather(*tasks)


if __name__ == '__main__':
    asyncio.run(main())
```

第二种

```
class Zhiwang:
    def __init__(self, n):
        pass
    @classmethod
    async def init_run(cls, number):
        self = cls(number)
        await self.__run()
        return self

    async def __run(self):
        pass


async def main():
    tasks = [asyncio.create_task(Zhiwang.init_run(i)) for i in [1, 2, 3, 4]]
    res = await asyncio.gather(*tasks)


if __name__ == '__main__':
    asyncio.run(main())
```

























