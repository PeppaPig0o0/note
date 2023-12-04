Tkinter
=======

引入tkinter

```python
import tkinter as tk
```

创建应用程序主窗口对象

```python
root = tk.Tk()
```

```
#进入事件循环
root.mainloop()
```

```python
# 正方形的绘制
import turtle  # 引入turtle库

turtle.setup(width=800, height=600, startx=0, starty=0)  # 设置画布的尺寸和位置
turtle.speed(2)  # 设置画笔的移动速度，范围1-10

angle = 90  # 正方形的角度为90°
length = 150  # 设置正方形的边长为150像素

turtle.pendown()  # 落笔
for i in range(4):  # 循环4次
    turtle.forward(length)  # 当前方向，移动一个边长的长度
    turtle.right(angle)  # 右转
turtle.penup()  # 提笔

turtle.done()  # 停止画笔绘制,但绘图窗体不关闭
```

```python
# 圆形的绘制
import turtle  # 引入turtle库

turtle.setup(width=800, height=600, startx=0, starty=0)  # 设置画布的尺寸和位置
turtle.speed(2)  # 设置画笔的移动速度，范围1-10

angle = 360  # 曲线的角度为360°
radius = 120  # 设置半径为120像素

turtle.pendown()  # 落笔
turtle.circle(radius, angle)  # 画出通过半径和角度画出曲线。第一参数为半径，第二参数为角度，第三参数为步长（可选）
turtle.penup()  # 提笔

turtle.done()  # 停止画笔绘制,但绘图窗体不关闭
```

```python
# 阶梯的绘制
import turtle  # 引入turtle库

turtle.setup(width=800, height=600, startx=0, starty=0)  # 设置画布的尺寸和位置
turtle.speed(2)  # 设置画笔的移动速度，范围1-10

angle = 90  # 设置阶梯的角度为90°
length = 100  # 设置阶梯的边长为100像素

turtle.penup()  # 提笔
turtle.goto(-300, 200)  # 将画笔移动到(-300,200)坐标位置

turtle.pendown()  # 落笔
for i in range(4):  # 循环4次
    turtle.forward(length)  # 当前方向，移动一个边长的长度
    turtle.right(angle)  # 右转
    turtle.forward(length)  # 当前方向，移动一个边长的长度
    turtle.left(angle)  # 左转
turtle.penup()  # 提笔

turtle.done()  # 停止画笔绘制,但绘图窗体不关闭
```

```

```

