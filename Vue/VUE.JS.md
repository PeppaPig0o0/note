Vue.JS
======



Vue的特点
---------

1、采用组件化模式，提高代码复用率，让代码更好维护

2、声明式编码，让编码人员无需直接操作DOM，提高开发效率

3、使用虚拟DOM+Diff算法



Vue安装
-------

1、下载安装https://cn.vuejs.org/  

```javascript
<script src = "js/vue.js"></script>
```

2、下载Vue-devtools  https://github.com/vuejs/vue-devtools

3、关闭Vue启动时的生产提示

```vue
<script type="text/javascript">
    Vue.config.productionTip = false
</script>
```



Vue核心
-------

#### 1.1初识Vue

```vue
    <div id="root">
        <h1>
            hello,{{ name }}
        </h1>
    </div>

    <script type="text/javascript">
        #关闭提示
        Vue.config.productionTip = false
        new Vue({
            el : '#root' ,//el指定当前Vue为哪个容器服务，通常为CSS选择器
                        //el : document.getElementById('root') 也可以这样写
            data : { //data中存储数据，供el指定的容器使用
                name:'我',
            }
        })
   </script>
```

​	**容器和Vue实例之间只能一对一**

​	**{{   }}     双花括号之间，只能放JS表达式和JS代码**



#### 1.2模板语法

##### 	插值语法

​	{{      }}

​	一般用于标签体内

```vue
 
 {{  name  }}
 <script type="text/javascript">
        #关闭提示
        Vue.config.productionTip = false
        new Vue({
            el : '#root' ,//el指定当前Vue为哪个容器服务，通常为CSS选择器
                        //el : document.getElementById('root') 也可以这样写
            data : { //data中存储数据，供el指定的容器使用
                name:'我',
            }
        })
   </script>
```

##### 	指令语法

​	v-bind:      可以简写为:

​	一般用于解析标签（包含标签属性，标签体内容，绑定事件）

```vue
    <div id="root">
        你好，{{ name }},{{age}},{{Date.now()}}
        <a :href="url">点击</a>
        <a v-bind:href="url"></a>
    </div>
```

