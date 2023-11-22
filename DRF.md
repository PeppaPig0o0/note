视图
====

APIView
-------

GenericAPIView
--------------

GenericAPIView+5个Mixin视图扩展类
---------------------------------

GenericAPIView的9个视图子类 
----------------------------

```
CreateAPIView,ListAPIView
RetrieveAPIView,DestoryAPIView,UpdateAPIView 

ListCreateAPIView, RetrieveUpdateDestroyAPIView, RetrieveUpdateAPIView, RetrieveDestroyAPIView
```

viewset 
--------

过滤、排序、分页
================

过滤
----

### 使用内置过滤

这两种方式没生效

```python
# 第一种
# setting.py中
REST_FRAMEWORK = {
	...
    'DEFAULT_FILTER_BACKENDS': ['rest_framework.filters.SearchFilter'],
}
```

```python
#第二种
from rest_framework.filters import SearchFilter
class ProjectViews(GenericAPIView):
    ...
    filter_backends = [SearchFilter]
```

### 第三方django-filter

```
pip install django-filter
```

```
# 需要注册应用，setting.py中
INSTALLED_APPS = [
    ...
    'django_filters',  
]
```

```python
# 局部中应用
from django_filters.rest_framework import DjangoFilterBackend # 导入第三方过滤类

class StudentList_V4(ListCreateAPIView):
    queryset = Student.objects.filter(is_delete=False)
    serializer_class = StudentSerializer
    filter_backends = [DjangoFilterBackend, ]
    filterset_fields = ['name', 'age', 'gender']  # 指定过滤的字段
```

```python
# 全局中应用
REST_FRAMEWORK = {
    'DEFAULT_FILTER_BACKENDS': ('django_filters.rest_framework.DjangoFilterBackend',)
}
```

```
请求
http://120.46.209.92/drf/student/v4?gender=1
```

排序
----

```python
from rest_framework.filters import OrderingFilter

class StudentList_V4(ListCreateAPIView):
    queryset = Student.objects.filter(is_delete=False)
    serializer_class = StudentSerializer
    filter_backends = [DjangoFilterBackend, OrderingFilter]
    filterset_fields = ['name', 'age', 'gender']  # 指定过滤的字段
    ordering_fields = ('age',)
```

序列化
======

serializers.py
--------------

```python
from rest_framework import serializers
from .models import *

class StudentSerializer(serializers.ModelSerializer):
    # 定义字段验证条件，反序列化时进行验证字段
    gender_name = serializers.ReadOnlyField(source='get_gender_display')
    name = serializers.CharField(max_length=4,min_length=2)
    age = serializers.IntegerField(max_value=200, min_value=0)
    gender = serializers.ChoiceField(choices=(1,2))
    class Meta:
        # 指定模型类
        model = Student
        # 序列化的域有哪些，也可以写成列表['name','age'','gender']
        fields = '__all__' 
```

反序列化字段校验
----------------

### 校验字段

```python
# 反序列化是验证的字段属性
max_length 最大长度
min_length 最小长度
allow_blank 是否允许为空（默认不能为空）
trim_whitespace 是否截断空白字符(去除字符串前后空格)
max_value 最大值
min_value 最小值
```

```
# 通用参数
read_only   True/False
write_only  True/False
required    True/False  在反序列化时必须输入，默认True
default    	反序列化时默认值
allow_null  True/False  是否允许传入None
validators  使用的验证器
error_messages  字典 包含错误编号与错误信息的
label       用HTML展示API时，显示的字段名字
help_text   用HTML展示API时，显示的字段帮助提示信息
```



### 校验钩子函数

```python
# 1.局部验证，只验证一个字段 validate_  +  字段名
def validate_age(self,data):
	if data<150:
		return data
	raise ValidationError('年龄不能超过300')
```

```python
# 2.全局认证
# 所有的字段都可以在这里写规则验证
    def validate(self, validate_data):
        name = validate_data.get('name')
        age = validate_data.get('age')
        if age>200 or age<0:
            raise ValidationError('...')
        return validate_data
```

```python
# 3.定义函数
def check_age(age):
    if age > 200 or age < 0:
        raise ValidationError('...')
    return age

class StudentSerializer(serializers.ModelSerializer):
    age = serializers.IntegerField(validators=[check_age,])
```

### 额外参数

```python
fields = '__all__'
extra_kwargs = {
    	'age':{'read_only': True, }
    }
```



fields
------

```python
class Meta:
    # 指定模型类
    model = Student
    # 序列化的域有哪些，也可以写成列表['name','age'','gender']
    fields = '__all__' 
    # 只展示特定的域
    fields = ('name', 'age')
    # 不展示这些域（exclude不能和fields 一同使用）
    exclude = ('name', 'age')
    
    # 可以以额外参数写入
    fields = '__all__'
    
```

路由
====

路由
----

```python
# urls.py
urlpatterns = [
    # 1.第一种
    path('student/v4', views.StudentList_V4.as_view(), name='student_v4'),
    path('student/v4/<int:pk>', views.StudentDetail_V4.as_view(), name='student_v4'),
	
    # 2.第二种，如果视图类继承了ViewSetMixin类，根据请求类型匹配对应方法
    path('student/v5', views.StudentView_V5.as_view({'get':'list','post':'create'}), name='student_v5'),
    path('student/v5/<int:pk>', views.StudentView_V5.as_view({'get':'retrieve','delete':'destroy','put':'update'}), name='student_v5'),
]
```

```python
# 3.第三种，如果视图类继承了ModelViewSet的写法——使用SimpleRouter()
from rest_framework import routers

router = routers.SimpleRouter()
# router.register('前缀','视图类')
router.register('student/v5', views.StudentView_V5, 'student_v5')
# 如果需要注册多个
# router.register('student/v5', views.StudentView_V5, 'student_v5')

urlpatterns = [
    ...
    ...
]

urlpatterns += router.urls
```

如果打印 print(router.urls)

```
# 结果：
[
<URLPattern '^student/v5/$' [name='student_v5-list']>, 
<URLPattern '^student/v5/(?P<pk>[^/.]+)/$' [name='student_v5-detail']>
]

```

```python
# 4.第四种，如果视图类继承了ModelViewSet的写法————使用DefaultRouter()
router = routers.DefaultRouter()
# 和第三种用法一样
```

如果打印 print(router.urls)

```python
[
<URLPattern '^student/v5/$' [name='student_v5-list']>, 

<URLPattern '^student/v5\.(?P<format>[a-
z0-9]+)/?$' [name='student_v5-list']>, 

<URLPattern '^student/v5/(?P<pk>[^/.]+)/$' [name='student
_v5-detail']>, 

<URLPattern '^student/v5/(?P<pk>[^/.]+)\.(?P<format>[a-z0-9]+)/?$' [name='student
_v5-detail']>, 

<URLPattern '^$' [name='api-root']>, 

<URLPattern '^\.(?P<format>[a-z0-9]+)/?$' [name='api-root']>
]

# 可以在url后面加入.json展示数据
# 可以访问根，输出根路径
```

action
------

给继承自ModelViewSet的视图类中 自定义的函数 添加路由

```python
from rest_framework.decorators import action

# 在视图类中 定义
# methods 请求方式，  detail:True单条查询，带pk
@action(methods=['GET'], detail=False)
def get_2(self, request):
    students = self.get_queryset()
    serializer = self.get_serializer(students, many=True)
    return Response(serializer.data, status=status.HTTP_200_OK)
```

```python
# 生成的路由
[<URLPattern '^student/v6/$' [name='student_v6-list']>,
<URLPattern '^student/v6/get_2/$' [name='student_v6-get-2']>, 
<URLPattern '^student/v6/(?P<pk>[^/.]+)/$' [name='student_v6-detail']>]
```

```python
# 生成请求地址
http://127.0.0.1/drf/student/v6/get_2
```

认证
====

jwt
---

### 原理

```python
'''
jwt   json web token
三段式： 头.体.签名    head.payload.sign
1) jwt分三段式:头.体.签名 (head.payload;sgin)
2)头和体是可逆加密，让服务器可以反解出user对象，签名是不可逆加密，保证整个token的安全性的
3)头体签名三部分，都是采用json格式的字将串，进行加密，可逆加密一般采用base64算法，不可逆加密一般采用hash(md5)算法
4)头中的内容是基本信息: 公司信息、项目组信息、token采用的加密方式信息
"company":"公司信息”
.
5) 体中的内容是关键信息: 用户主键、用户名、签发时客户端信息(设备号、地址)、过期时间
"user_id": 1
e..
6)签名中的内容时安全信息: 头的加密结果 + 体的加密结果 + 服务器不对外公开的安全码 进行md5加密
"head":"头的加密字符串"
"payload":“体的加密字符串”
"secret_key":"安全码"

校验：
1)将token按 . 拆分为三段字符串，第一段 头加密字符串 一般不需要做任何处理
2)第二段 体加密字符串，要反解出用户主键，通过主键从User表中就能得到登录用户，过期时间和设备信息都是安全信息，确保token没过期，且是同一设备来的。
3)再用第一段 + 第二段 + 服务器安全码 不可逆md5加，与第三段名字 进行碰撞校验，通过后才能代表第
二段校验得到的user对象就是合法的登录用户
'''
```

### drf中jwt认证流程

```python
'''
1)用账号密码访问登录接口，登录接口逻辑中调用 签发token 算法，得到token，返回给客户端，客户端自己存到cookies中

2)校验token的算法应该写在认证类中(在认证类中调用)，全局配器给认证组件，所有视图类请求，都会进行认证校验所以请求带了token，就会反解出user对象，在视图类中用request.user就能访问登录的用户
注: 登录接口需要做 认证 + 权限 两个局部禁用
'''
```

### drf-jwt安装和使用

#### 第一步

```
pip install djangorestframework-jwt
```

```python
# setting.py中加入           app名字.model类名小写
AUTH_USER_MODEL = 'drf.user'
```

```python
from django.contrib.auth.models import AbstractUser
class User(AbstractUser):
    phone = models.CharField(max_length=11)
    name = models.CharField(max_length=32)
    
# 然后迁移数据库
```

**以上这些步骤最好在创建一个新项目后使用。**

#### 第二步：简单使用

```
# 创建超级用户
python3 manage.py createsuperuser
```

```python
# 项目urls.py中
from rest_framework_jwt.views import ObtainJSONWebToken,RefreshJSONWebToken,VerifyJSONWebToken
urlpatterns = [
    ...
    re_path(r'^login/?', ObtainJSONWebToken.as_view(), name='login')
]
```

现在可以通过postman 发送post请求进行登录获取token

```python
from rest_framework_jwt.authentication import JSONWebTokenAuthentication
# 视图开启局部认证
class StudentView_V7(ModelViewSet):
    authentication_classes = [JSONWebTokenAuthentication,]
    ...
    ...
```

postman中  接口必须 headers中加入Authorization字段，值：jwt  + token可以开启认证，否则不认证。！！所以要在jwt基础上自定义认证。

#### 第三步，自定义

因为JWT认证必须要在请求中添加 Authorization字段才会认证，否则不认证直接通过。

所以不使用它，自己写一个基于第三方JWT的认证类，这样请求头中没有Authorization字段，就会认证失败。

```python
# app中新建一个auth.py文件
import jwt
from rest_framework_jwt.authentication import BaseJSONWebTokenAuthentication
from rest_framework_jwt.utils import jwt_decode_handler
from rest_framework import exceptions
from rest_framework_jwt.authentication import jwt_decode_handler


class MyAuth(BaseJSONWebTokenAuthentication):
    # 获取jwt的token值
    def authenticate(self, request):
        jwt_value = request.META.get('HTTP_AUTHORIZATION')
        if not jwt_value:
            raise exceptions.AuthenticationFailed('Authorization字段是必须的')
        try:
            # 认证
            payload = jwt_decode_handler(jwt_value)
        except jwt.ExpiredSignature:
            raise exceptions.AuthenticationFailed('token过期')
        except jwt.DecodeError:
            raise exceptions.AuthenticationFailed('解码错误')
        except jwt.InvalidTokenError:
            raise exceptions.AuthenticationFailed('token无效')
        # 获取用户对象
        user = self.authenticate_credentials(payload)
        return user, jwt_value

```

```python
# 
from ..auth import MyAuth
class StudentView_V7(ModelViewSet):
    authentication_classes = [MyAuth,]
```

权限
====

自定义权限
----------

```

```

