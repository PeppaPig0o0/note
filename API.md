API
===

一、Ajax请求，前端参考demo
--------------------------

```html
<!--    onsubmit="return false"  设置不通过表单提交数据-->
    <form id = "form1" action="##" method="post" onsubmit="return false">
        <table width="600px" align="center" height="500px">
            <tr>
                <td align="right" width="250px">用户名：</td>
                <td><input type="text" id="username"></td>
            </tr>
            <tr>
                <td align="right">密码：</td>
                <td><input type="password" id="pwd"></td>
            </tr>
            <tr>
                <td align="right">姓名：</td>
                <td><input type="text" id="realname"></td>
            </tr>
            <tr>
                <td align="right">电话：</td>
                <td><input type="text" id="tel"></td>
            </tr>
            <tr>
                <td align="right">地址：</td>
                <td><input type="text" id="address"></td>
            </tr>
            <tr>
                <td align="right">出生日期：</td>
                <td><input type="date" id="birth"/></td>
            </tr>
            <tr>
                <td align="right">性别：</td>
                <td><input type="radio" value="男" id="sex" name="sex" checked/>
                    <span>男</span>
                    <input type="radio" name="sex" value="女"/>
                    <span>女</span>
            </tr>

            <tr>
                <td colspan="2" align="center" bgcolor="#398bff">
                    <input type="submit" onclick="register()">
                    <input type="reset" />
                </td>
            </tr>
        </table>
    </form>
```

### 注册

```javascript
<script type="text/javascript">
            function register(){
                var json = {
                        "username" : $('#username').val(),         //用户名，必要
                        "pwd"      : $('#pwd').val(),              //密码，必要
                        "realname" : $('#realname').val(),         //姓名，非必要
                        "sex"      : $('#sex').val(),              //性别，非必要
                        "birth"    : $('#birth').val(),            //出生年月日1999-05-03，非必要
                        "tel"      : $('#tel').val() ,             //电话，非必要
                        "address"  : $('#address').val()           //地址，非必要
                }
                $.ajax({
                    url: "http://117.159.165.168:54380/register",  //请求URL
                    type: "post",                                  //请求方式POST
                    dataType: "json",                              //数据类型json
                    data: json,                                    //数据体
                    success: function (result) {                   //请求成功返回值
                        console.log(result);
                        alert(result.msg);
                    },
                    error: function (result){                      //请求失败返回值
                        alert('网络错误，请联系汤地');
                    },
                })
            }
</script>
```

### 登录

```javascript
<script type="text/javascript">
            function login(){
                var json = {
                        "username" : $('#username').val(),         //用户名，必要
                        "pwd"      : $('#pwd').val()               //密码，必要
                }
                $.ajax({
                    url: "http://117.159.165.168:54380/login",     //请求URL
                    type: "post",                                  //请求方式POST
                    dataType: "json",                              //数据类型json
                    data: json,                                    //数据体
                    success: function (result) {                   //请求成功返回值
                        console.log(result);
                        alert(result.msg);
                    },
                    error: function (result){                      //请求失败返回值
                        alert('网络错误，请联系汤地');
                    },
                })
            }
</script>
```



二、后端Django
--------------

```python
from django.shortcuts import render,HttpResponse
import json
import api.models
from api.models import *
# Create your views here.
def register(request):
    res = {}
    if request.method=='POST':
        response =request.POST.dict()
        username = response.get('username', 0),
        if check_uname(response) :
            res['status'] = '400'
            res['msg'] = '用户名已存在'
        else:
            ApiUser.objects.create(
                username = response.get('username'),
                realname = response.get('realname','无名'),
                pwd = response.get('pwd',123456),
                sex = response.get('sex','1'),
                birth = response.get('birth','2000-01-01'),
                address = response.get('address','无地址'),
                tel = int(response.get('tel',13000000000))
            )
            res['status'] = '200'
            res['msg'] = '注册成功，API来自汤地的Django服务器，吃井不忘挖水人'
    else:
        res['status'] = '403'
        res['msg'] = '该接口只接收POST请求'

    res = json.dumps(res,ensure_ascii=False)
    return HttpResponse(res)

def login(request):
    res = {}
    if request.method == 'POST':
        response = request.POST.dict()
        if not check_uname(response):
            res['status'] = '400'
            res['msg'] = '用户名不存在'
        else:
            user_info = check_password(response)
            if user_info:
                res['status'] = '200'
                res['msg'] = '登录成功，API来自汤地的Django服务器，吃井不忘挖水人'
                user_info = check_password(response)
                res['data']['realname'] = user_info[1]
                res['data']['tel'] = user_info[4]
                res['data']['sex'] = user_info[5]
                res['data']['birth'] = user_info[6]
                res['data']['address'] = user_info[7]
            else:
                res['status'] = '401'
                res['msg'] = '密码错误'
    else:
        res['status'] = '403'
        res['msg'] = '该接口只接收POST请求'

    res = json.dumps(res, ensure_ascii=False)
    return HttpResponse(res)


def check_uname(response):
    username = response['username']
    res = ApiUser.objects.filter(username=username)
    return 1 if res else 0

def check_password(response):
    username = response['username']
    res = ApiUser.objects.filter(username=username)
    return res if res['pwd']==response['pwd'] else 0
```

