一、Python 数据采集部分
-----------------------

​	本部分是Python可视化第一部分，使用Python原生语法，采集数据来自于 http://www.air-level.com/ 网站。

​	采集内容：

​	首页：数据更新时间、城市名、城市对应URL

​	二级页面：城市AQI（空气质量指数）、城市AQG（空气质量等级）、监测站、站点AQI、站点AQG、站点PM2.5值、站点PM10值、站点首要污染物

​	本部分共包含8个文件

### main.py

​	main()，爬虫调用入口。

​	get_index_data(url,headers)  对首页信息进行采集，并调用二级页面采集方法

​	get_uncall_citys()  对未响应URL进行再次采集，并调用二级页面采集方法

### sqlcommit.py

​	import pymysql  #使用pymysql库，链接mysql数据库

​	类 SqlCommit主要实现数据库操作

```python
class SqlCommit:
    db = ''  # 数据库链接
    Cursor = ''
    
    #数据库连接，获取游标
    def __init__(self):
        SqlCommit.db = pymysql.connect(host='127.0.0.1', user='root', password='123456', database='yq')  # 数据库链接
        SqlCommit.Cursor = SqlCommit.db.cursor()
        
	#提交sql语句
    def sqlcommit(self,sql):
        SqlCommit.Cursor.execute(sql)
        SqlCommit.db.commit()
        
	#接受sql语句并查询返回各城市数据
    def select(self,sql):
        res = SqlCommit.Cursor.execute(sql)
        res = SqlCommit.Cursor.fetchall()
        return res

    #关闭数据库链接
    def close_db(self):
        SqlCommit.db.close()
	
    #清空uncall_urls表
    def delete_uncall_urls_table(self):
        sql_delete_uncall_urls_table = 'DELETE FROM echarts_uncallurls'
        SqlCommit.Cursor.execute(sql_delete_uncall_urls_table)
        SqlCommit.db.commit()
```



### sencondpage.py

​	类SecondPage，实现主要数据的采集

​	

### error_w_log.py

### setheader.py

### province_city.py

### time_transform.py

### api_error_log.txt







