Echarts
========

上手Echarts
-----------

1. 引入echarts.js文件
2. 准备一个呈现图表的盒子（容器、div）
3. 初始化echarts实例对象
4. 准备配置项
5. 将配置项设置给echarts实例对象

option配置
----------

### title

标题

- show——true/false是否展示标题
- text——标题文本，支持用\n换行
- link——文本超链接
- target——blank\self 新窗口或当前窗口打开链接

- textStyle

	- color——文字颜色

	- fontStyle——文字字体风格normal / italic / oblique 正常/斜体

	- fontWeight——文字粗细normal/blod/bloder/lighter/100/200/等数字

	- fontsize——文字大小

- subtext——副标题，具体用法参考title

- textAlign——水平对齐  auto/left/right/center

- textVerticalAlign 垂直对齐 auto/top/bottom/middle

	*其他用法请参考配置项手册*

### legend 图例

type——plain/scroll  默认plain 图例较多时scroll可以滚动

left/top/right/bottom——距离容器的距离：像素值/百分比/auto

width/height——图例宽和高 默认auto / 数字

orient——布局 horizontal/vertical  水平或垂直

selected——图例的选中状态

formatter——‘你好{name}’   或  function(){}

selectedMode——true/false能够选择图例   single/multiple单选或多选



### xAxis和yAxi

x和y坐标轴设置

type坐标轴类型:

- category  类目轴
- value  数值轴

### series

series是一个列表，列表中每一项是一个字典

- name 系列的名称

- type 图表类型：！！

	bar柱形图，line折线图，pie饼图，scatter散点图，

- data 数据，列表

- title 标题

图表
----

### 柱状图

type:’bar’

最大值和最小值

```javascript
markPoint:{
    data:[
        {type:'max', name:'最大值'},
        {type:'min', name:'最小值'},
    ]
},
```

平均值 markLine

```javascript
markLine:{
    data:[
    	{type:'average', name:'平均值'}
    ]
}
```

数值显示

```javascript
label :{
    show:true, # 显示数值
    rotate:60  # 旋转角度
}
```

柱宽度

```
barWidth:'50%'
```

横向柱状图

xAxis 和 yAxis内容互换