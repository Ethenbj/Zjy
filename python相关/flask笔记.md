# flask

## **任务四：数据可视化**

### **4.1新建**Flask可视化项目

项目命名为ViewData， 项目中新建文件夹app/models存放数据库映射类，app/static/js存放js的外部工具包，app/templates/main存放html前端页面，app/templates/errors存放404之后的html前端页面，app/views存放SQLAlchemy的语法的数据库访问类。

![img](file:///C:\Users\zheng\AppData\Local\Temp\ksohtml16708\wps1.jpg) 

### 4.2 **新建app/__init__.py**

新建flask的初始化类，自定义函数def  create_app(config_name)封装一个方法，专门用于创建Flask实例；自定义函数def  config_errorhandler(app)编写访问失败之后的404页面的跳转设置。

```python
from flask import Flask, render_template
from app.config import config
from app.extensions import config_extensions
from app.views import config_blueprint

# 封装一个方法，专门用于创建Flask实例
def create_app(config_name): # development
    # 创建应用实例
    app = Flask(__name__)
    # 初始化配置
    app.config.from_object(config.get(config_name) or config['default'])
    # 调用初始化函数
    config[config_name].init_app(app)
    # 配置扩展
    config_extensions(app)
    # 配置蓝本
    config_blueprint(app)
    # 错误页面定制
    config_errorhandler(app)
    # 返回应用实例
    return app


def config_errorhandler(app):
    # 如果在蓝本定制，只针对本蓝本的错误有效，
    # 可以使用app_errorhandler定制全局有效的错误显示
    @app.errorhandler(404)
    def page_not_found(e):
        return render_template('errors/404.html')
```



### 4.3新建app/config.py**

新建flask连接数据库的配置类，将flask框架连接数据库的配置编写成函数，包括数据库的ip、端口、用户名、密码等。

```python
import os

base_dir = os.path.abspath(os.path.dirname(__file__))


# 通用配置
class Config:
    # 秘钥
    SECRET_KEY = os.environ.get('SECRET_KEY') or '123456'
    # 数据库
    SQLALCHEMY_COMMIT_ON_TEARDOWN = True
    SQLALCHEMY_TRACK_MODIFICATIONS = False

    # 额外的初始化操作，即使什么内容都没有写，也是有意义的
    @staticmethod
    def init_app(app):
        pass


# 开发环境   语法：mysql+pymysql://用户名：密码@ip：端口/数据库名
class DevelopmentConfig(Config):
    SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:root@localhost:3306/visiondata'


# 测试环境
class TestingConfig(Config):
    SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:root@localhost:3306/visiondata'


# 生产环境
class ProductionConfig(Config):
    SQLALCHEMY_DATABASE_URI = 'mysql+pymysql://root:root@localhost:3306/visiondata'


# 配置字典
config = {
    'development': DevelopmentConfig,
    'testing': TestingConfig,
    'production': ProductionConfig,

    'default': DevelopmentConfig
}
```



### 4.4 **新建app/ extensions.py**

导入flask相关模块，创建SQLAlchemy对象并初始化Bootstrap。自定义函数def config_extensions(app)完成SQLAlchemy和Bootstrap的初始化

```python
# 导入类库
from flask_bootstrap import Bootstrap
from flask_sqlalchemy import SQLAlchemy
from flask_migrate import Migrate
from flask_moment import Moment


# 创建对象
bootstrap = Bootstrap()
db = SQLAlchemy()
moment = Moment()
migrate = Migrate(db=db)



# 初始化
def config_extensions(app):
    bootstrap.init_app(app)
    db.init_app(app)
    moment.init_app(app)
    migrate.init_app(app)
```



### 4.5 **编辑****app/models/****表名.****py**

编写flask的数据库表的映射类，每一个表格对应一个文件，映射类命名与表名一致。

表bigdata_work

```python
from app.extensions import db


class BigDataWork(db.Model):
    __tablename__ = 'bigdata_work'
    id = db.Column(db.Integer, primary_key=True)
    job_name = db.Column(db.String(255))
    company_name = db.Column(db.String(255))
    city = db.Column(db.String(255))
    job_require = db.Column(db.Text)
    recruit_number = db.Column(db.String(255))
    money = db.Column(db.String(255))
    skill_require = db.Column(db.String(255))
    release_date = db.Column(db.String(255))
    sex = db.Column(db.String(255))
    company_detail = db.Column(db.String(255))
    education = db.Column(db.String(255))
```



表skill_require

 ```python
from app.extensions import db


class SkillRequire(db.Model):
    __tablename__ = 'skill_require'
    id = db.Column(db.Integer, primary_key=True)
    skill = db.Column(db.String(255))
    frequency = db.Column(db.Integer)
 ```



### **4.6编辑app/models/__init__.py**

编写flask的访问数据库的初始化文件，导入数据库的映射表类

```python
from app.extensions import db

from .bigdata_work import BigDataWork
from .skill_require import SkillRequire
```



### 4.7 **新建****app/****static****/****js**

将html前段需要用到的js工具包存放在js文件夹下。



### 4.8 **新建ViewData/mamage.py**

新建flask启动类，创建flask实例和数据库迁移命令，以及程序的入口。

```python
import os
from app import create_app
from flask_script import Manager, Server
from flask_migrate import MigrateCommand

# 获取配置
config_name = os.environ.get('FLASK_CONFIG') or 'default'
# 创建Flask实例
app = create_app(config_name)

# 创建命令行启动控制对象
manager = Manager(app)

manager.add_command("runserver", Server(use_debugger=True))

# 添加数据库迁移命令
manager.add_command('db', MigrateCommand)

# 启动项目
if __name__ == '__main__':
    manager.run()
```



### 4.9 **新建app/views/__init__.py** 

新建flask的蓝本配置初始化类，进行蓝本的配置，自定义函数def config_blueprint(app)以及封装函数完成蓝本的注册。

```python
from .main import main


# 蓝本配置
DEFAULT_BLUEPRINT = (
    # 蓝本，前缀
    (main, ''),
)


# 封装函数，完成蓝本注册
def config_blueprint(app):
    for blueprint, prefix in DEFAULT_BLUEPRINT:
        app.register_blueprint(blueprint, url_prefix=prefix)
```



### 4.10 **新建****app/views****的****main.py**

（1） 分析并统计招聘数量"大数据"相关职位招聘数量,分别使用柱状图表达，同时在网页后台输出相关数据打印语句。

**数据源：**数据库visiondata中bigdata_work

在app/views下的main.py添加函数def get_big_data_work()，使用SQLAlchemy语法编写sql的查询语句，进行相同职位进行数量汇总，"大数据"相关职位招聘数量比较

 ```python
# 对相同职位进行数量汇总，"大数据"相关职位招聘数量比较
def get_big_data_work():
    # sql: select job_name,count(*) from bigdata_work where job_name like '%大数据%' GROUP BY job_name
    gbdw_rs = db.session.query(BigDataWork.job_name, func.count('*').label('job_count'))\
        .group_by(BigDataWork.job_name).order_by(desc('job_count')).all()
    print('"大数据"相关职位招聘数量：' + str(gbdw_rs))
    return gbdw_rs
 ```



（2）请根据数据库表格bigdata_work，以招聘大数据相关岗位数量为依据绘制散点热度地图。同时在网页后台输出相关数据打印语句。

```python
# 对相同工作地点进行数量汇总，各城市"大数据"相关职位招聘数量   获取的结果 ('上海', 2239)
def get_city_work():
    # sql:  select job_name,city,count(job_name) from bigdata_work GROUP BY job_name,city
    gcw_rs = db.session.query(BigDataWork.city, func.count('*').label('job_count')).group_by(BigDataWork.city).all()
    print('各城市"大数据"相关职位招聘数量：' + str(gcw_rs))
    return gcw_rs
```



**数据源：**数据库visiondata中bigdata_work表

在app/views下的main.py添加函数def get_city_work()，使用SQLAlchemy语法编写sql的查询语句，对相同工作地点进行数量汇总，各城市"大数据"相关职位招聘数量。 

 

（3）请统计2018年，2019年各月份大数据相关职位的招聘数量，以左右分布的（左侧2018年度，右侧2019年度）饼图进行表达

**数据源：**数据库visiondata中avg_money_city和bigdata_work

在app/views下的main.py添加函数get_month_work()，使用SQLAlchemy语法编写sql的查询语句，查询大数据相关职位招聘数量(按月分组）

 ```python
# "大数据"相关职位招聘数量(按月分组）
def get_month_work():
    # sql:  select release_date,count(id),DATE_FORMAT(release_date,'%m月') from bigdata_work group by DATE_FORMAT(release_date,'%Y%m') HAVING year(release_date)=2018
    # func.count(BigDataWork.id).label('job_count')  调用count行数  统计  id的总数  映射名为 job_name
    # func.DATE_FORMAT(BigDataWork.release_date, '%m月').label('month'))  date_format函数  获取月份  映射名为 month
    gmw_rs_2018 = db.session.query(BigDataWork.release_date, func.count(BigDataWork.id).label('job_count'),
                                   func.DATE_FORMAT(BigDataWork.release_date, '%m月').label('month'))\
        .group_by(func.DATE_FORMAT(BigDataWork.release_date, '%Y%m'))\
        .having(func.year(BigDataWork.release_date) == 2018).all()
    gmw_rs_2019 = db.session.query(BigDataWork.release_date, func.count(BigDataWork.id).label('job_count'),
                                   func.DATE_FORMAT(BigDataWork.release_date, '%m月').label('month'))\
        .group_by(func.DATE_FORMAT(BigDataWork.release_date, '%Y%m'))\
        .having(func.year(BigDataWork.release_date) == 2019).all()
    #  将两个查询结果赋值给一个新的数据返回到前端
    gmw_rs = [gmw_rs_2018, gmw_rs_2019]
    print('"大数据"相关职位招聘数量(按月分组）：' + str(gmw_rs))
    return gmw_rs
 ```



（4）请根据指定表中的数据，分析各知识技能在某个招聘岗位能力需求中的占比情况，通过词云图例进行呈现。

**数据源：**数据库visiondata中skill_require

在app/views下的main.py添加函数def get_skill_require()，使用SQLAlchemy语法编写sql的查询语句，查询大数据相关职位技能标签词云。

 ```python
# "大数据"相关职位技能标签词云  获取结果
def get_skill_require():
    # sql: select * from skill_require
    gsr_rs = SkillRequire.query.all()
    print('"大数据"相关职位技能标签词云  获取结果成功')
    return gsr_rs


 ```



**app/views下的main.py完整代码，如下所示：**

```python
import json
from flask import Blueprint, render_template, jsonify
from app.models import BigDataWork, SkillRequire
from app.extensions import db
from sqlalchemy import *

main = Blueprint('main', __name__)  # 实例化路由


@main.route('/')
def index():
    return render_template('/main/echarts.html')    # 当url访问 / 直接跳转到主页


@main.route('/index/')      # url访问 /index/ 跳转主页  调用display方法  将查询结果通过render_template()传入html页面
def display():
    rs_gmw = get_month_work()
    rs_gsr = get_skill_require()
    rs_gcw = get_city_work()
    return render_template('/main/echarts.html', rs_gmw=rs_gmw, rs_gsr=rs_gsr, rs_gcw=rs_gcw)


# 对相同工作地点进行数量汇总，各城市"大数据"相关职位招聘数量   获取的结果 ('上海', 2239)
def get_city_work():
    # sql:  select job_name,city,count(job_name) from bigdata_work GROUP BY job_name,city
    gcw_rs = db.session.query(BigDataWork.city, func.count('*').label('job_count')).group_by(BigDataWork.city).all()
    print('各城市"大数据"相关职位招聘数量：' + str(gcw_rs))
    return gcw_rs


# "大数据"相关职位招聘数量(按月分组）
def get_month_work():
    # sql:  select release_date,count(id),DATE_FORMAT(release_date,'%m月') from bigdata_work group by DATE_FORMAT(release_date,'%Y%m') HAVING year(release_date)=2018
    # func.count(BigDataWork.id).label('job_count')  调用count行数  统计  id的总数  映射名为 job_name
    # func.DATE_FORMAT(BigDataWork.release_date, '%m月').label('month'))  date_format函数  获取月份  映射名为 month
    gmw_rs_2018 = db.session.query(BigDataWork.release_date, func.count(BigDataWork.id).label('job_count'),
                                   func.DATE_FORMAT(BigDataWork.release_date, '%m月').label('month'))\
        .group_by(func.DATE_FORMAT(BigDataWork.release_date, '%Y%m'))\
        .having(func.year(BigDataWork.release_date) == 2018).all()
    gmw_rs_2019 = db.session.query(BigDataWork.release_date, func.count(BigDataWork.id).label('job_count'),
                                   func.DATE_FORMAT(BigDataWork.release_date, '%m月').label('month'))\
        .group_by(func.DATE_FORMAT(BigDataWork.release_date, '%Y%m'))\
        .having(func.year(BigDataWork.release_date) == 2019).all()
    #  将两个查询结果赋值给一个新的数据返回到前端
    gmw_rs = [gmw_rs_2018, gmw_rs_2019]
    print('"大数据"相关职位招聘数量(按月分组）：' + str(gmw_rs))
    return gmw_rs


# "大数据"相关职位技能标签词云  获取结果
def get_skill_require():
    # sql: select * from skill_require
    gsr_rs = SkillRequire.query.all()
    print('"大数据"相关职位技能标签词云  获取结果成功')
    return gsr_rs
```



### 4.11 **新建****app****/****templates****/****errors****/404****.html**

编写url访问失败之后的提示页面。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
404
</body>
</html>
```



### 4.12 **新建app/templates/main/echarts.html**

新建/编辑app/templates/main/目录下的ehcarts.html文件，编写js获取到flask传过来的数据，通过echarts组件进行结果的展示。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>招聘信息统计</title>
    <!--  警告：这里一定要先引入ehcarts.js再引入china.js  不然会报错：ECharts is not Loaded  -->
    <script type="text/javascript" src="/static/js/echarts.js"></script>
    <script type="text/javascript" src="/static/js/china.js"></script>
    <script type="text/javascript" src="/static/js/jquery.min.js"></script>
    <script type="text/javascript" src="/static/js/js2wordcloud.js"></script>
</head>
<style>
    html , body , .content {
    width:100%;
    height:100%;
    padding: 0;
    margin: 0;
    box-sizing: border-box;
    background-color: #ccc;
}
.content {
    padding: 40px;
}
.header {
    height: 10%;
    width: 100%;
    font-size: 24px;
    font-weight: 700;
    line-height: 60px;
    text-align: center;
}
.body {
    height: 100%;
    width: 100%;
    text-align: center;
}
.chartBox {
    width: 100%;
    height: 60%;
    margin-bottom:40px;
}
</style>
<body>
    <div class="content">
        <div class="header"></div>
        <div class="body">
            <div class="chartBox" id="heatmap"></div>
            <div class="chartBox" id="mothWork"></div>
            <p>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
                大数据相关职位技能</p>
            <div class="chartBox" id="wordCloud"></div>
        </div>
    </div>
</body>
<script>
    //散点地图
    var heatMap = echarts.init(document.getElementById('heatmap'));
    var city_data =  [{% for r in rs_gcw %}{name:"{{r.city}}", value:"{{r.job_count}}"},{% endfor %}]
    var max_data = [{% for r in rs_gcw %}"{{r.job_count}}",{% endfor %}]
    var dataIntArr_max = [];        ///下面的函数用于将元素为字符串的数组转成元素为整型的数组
    max_data.forEach(function(data){
        dataIntArr_max.push(+parseInt(data));   ///遍历数组   将每个元素变成整型
    });
    var max = Math.max.apply(null, dataIntArr_max);  ////求得数组中最大的值  然后赋值给热力图作为热力图的最大值
    console.log("招聘“大数据”相关职位数量最大为" + max);

    //各大城市的地点坐标
    var geoCoordMap = {
        "海门":[121.15,31.89],"鄂尔多斯":[109.781327,39.608266],"招远":[120.38,37.35],"舟山":[122.207216,29.985295],
        "齐齐哈尔":[123.97,47.33],"盐城":[120.13,33.38],"赤峰":[118.87,42.28],"乳山":[121.52,36.89],
        "金昌":[102.188043,38.520089],"泉州":[118.58,24.93],"莱西":[120.53,36.86],"日照":[119.46,35.42],
        "胶南":[119.97,35.88],"南通":[121.05,32.08],"拉萨":[91.11,29.97],"云浮":[112.02,22.93],
        "梅州":[116.1,24.55],"文登":[122.05,37.2],"上海":[121.48,31.22],"攀枝花":[101.718637,26.582347],
        "威海":[122.1,37.5],"承德":[117.93,40.97],"厦门":[118.1,24.46],"汕尾":[115.375279,22.786211],
        "潮州":[116.63,23.68],"丹东":[124.37,40.13],"太仓":[121.1,31.45],"曲靖":[103.79,25.51],
        "烟台":[121.39,37.52],"福州":[119.3,26.08],"瓦房店":[121.979603,39.627114],"即墨":[120.45,36.38],
        "抚顺":[123.97,41.97],"玉溪":[102.52,24.35],"张家口":[114.87,40.82],"阳泉":[113.57,37.85],
        "莱州":[119.942327,37.177017],"湖州":[120.1,30.86],"汕头":[116.69,23.39],"昆山":[120.95,31.39],
        "宁波":[121.56,29.86],"湛江":[110.359377,21.270708],"揭阳":[116.35,23.55],"荣成":[122.41,37.16],
        "连云港":[119.16,34.59],"葫芦岛":[120.836932,40.711052],"常熟":[120.74,31.64],"东莞":[113.75,23.04],
        "河源":[114.68,23.73],"淮安":[119.15,33.5],"泰州":[119.9,32.49],"南宁":[108.33,22.84],
        "营口":[122.18,40.65], "惠州":[114.4,23.09],"江阴":[120.26,31.91],"蓬莱":[120.75,37.8],
        "韶关":[113.62,24.84],"嘉峪关":[98.289152,39.77313],"广州":[113.23,23.16],"延安":[109.47,36.6],
        "太原":[112.53,37.87],"清远":[113.01,23.7],"中山":[113.38,22.52],"昆明":[102.73,25.04],
        "寿光":[118.73,36.86],"盘锦":[122.070714,41.119997],"长治":[113.08,36.18],"深圳":[114.07,22.62],
        "珠海":[113.52,22.3],"宿迁":[118.3,33.96],"咸阳":[108.72,34.36],"铜川":[109.11,35.09],
        "平度":[119.97,36.77],"佛山":[113.11,23.05],"海口":[110.35,20.02],"江门":[113.06,22.61],
        "章丘":[117.53,36.72],"肇庆":[112.44,23.05],"大连":[121.62,38.92],"临汾":[111.5,36.08],
        "吴江":[120.63,31.16],"石嘴山":[106.39,39.04],"沈阳":[123.38,41.8],"苏州":[120.62,31.32],
        "茂名":[110.88,21.68],"嘉兴":[120.76,30.77],"长春":[125.35,43.88],"胶州":[120.03336,36.264622],
        "银川":[106.27,38.47],"张家港":[120.555821,31.875428],"三门峡":[111.19,34.76],"锦州":[121.15,41.13],
        "南昌":[115.89,28.68],"柳州":[109.4,24.33],"三亚":[109.511909,18.252847],"自贡":[104.778442,29.33903],
        "吉林":[126.57,43.87],"阳江":[111.95,21.85],"泸州":[105.39,28.91],"西宁":[101.74,36.56],
        "宜宾":[104.56,29.77],"呼和浩特":[111.65,40.82],"成都":[104.06,30.67],"大同":[113.3,40.12],
        "镇江":[119.44,32.2],"桂林":[110.28,25.29],"张家界":[110.479191,29.117096],"宜兴":[119.82,31.36],
        "北海":[109.12,21.49],"西安":[108.95,34.27],"金坛":[119.56,31.74],"东营":[118.49,37.46],
        "牡丹江":[129.58,44.6],"遵义":[106.9,27.7],"绍兴":[120.58,30.01], "扬州":[119.42,32.39],
        "常州":[119.95,31.79],"潍坊":[119.1,36.62],"重庆":[106.54,29.59],"台州":[121.420757,28.656386],
        "南京":[118.78,32.04],"滨州":[118.03,37.36],"贵阳":[106.71,26.57],"无锡":[120.29,31.59],
        "本溪":[123.73,41.3],"克拉玛依":[84.77,45.59],"渭南":[109.5,34.52],"马鞍山":[118.48,31.56],
        "宝鸡":[107.15,34.38],"焦作":[113.21,35.24],"句容":[119.16,31.95],"北京":[116.46,39.92],
        "徐州":[117.2,34.26],"衡水":[115.72,37.72],"包头":[110,40.58],"绵阳":[104.73,31.48],
        "乌鲁木齐":[87.68,43.77],"枣庄":[117.57,34.86],"杭州":[120.19,30.26],"淄博":[118.05,36.78],
        "鞍山":[122.85,41.12],"溧阳":[119.48,31.43],"库尔勒":[86.06,41.68],"安阳":[114.35,36.1],
        "开封":[114.35,34.79],"济南":[117,36.65],"德阳":[104.37,31.13],"温州":[120.65,28.01],
        "九江":[115.97,29.71], "邯郸":[114.47,36.6],"临安":[119.72,30.23],"兰州":[103.73,36.03],
        "沧州":[116.83,38.33],"临沂":[118.35,35.05],"南充":[106.110698,30.837793],"天津":[117.2,39.13],
        "富阳":[119.95,30.07],"泰安":[117.13,36.18],"诸暨":[120.23,29.71],"郑州":[113.65,34.76],
        "哈尔滨":[126.63,45.75],"聊城":[115.97,36.45],"芜湖":[118.38,31.33],"唐山":[118.02,39.63],
        "平顶山":[113.29,33.75],"邢台":[114.48,37.05],"德州":[116.29,37.45],"济宁":[116.59,35.38],
        "荆州":[112.239741,30.335165],"宜昌":[111.3,30.7],"义乌":[120.06,29.32],"丽水":[119.92,28.45],
        "洛阳":[112.44,34.7],"秦皇岛":[119.57,39.95],"株洲":[113.16,27.83],"石家庄":[114.48,38.03],
        "莱芜":[117.67,36.19],"常德":[111.69,29.05],"保定":[115.48,38.85],"湘潭":[112.91,27.87],
        "金华":[119.64,29.12], "岳阳":[113.09,29.37],"长沙":[113,28.21],"衢州":[118.88,28.97],
        "廊坊":[116.7,39.53],"菏泽":[115.480656,35.23375],"合肥":[117.27,31.86],"武汉":[114.31,30.52],
        "大庆":[125.03,46.58]
    };
     //函数用于将数据保存成ehcarts地图需要的数据格式  [name:'地点',value:[地图坐标,数据]]   [121.48, 31.22, "2239"]
    var convertData = function (data) {
        var res = [];
        for (var i = 0; i < data.length; i++) {
            var geoCoord = geoCoordMap[data[i].name];
            if (geoCoord) {
                res.push({
                    name: data[i].name,
                    value: geoCoord.concat(data[i].value)
                });
            }
        }
        return res;
    };
    mapOption = {
        title: {
            text:'各城市"大数据"相关职位招聘数量',
            x:'center',
                        //modified 0523

            subtextStyle:{
                //样式与主标题   方式指定就可以
                color:'#f30008',
                //字体风格,'normal','italic','oblique'
                fontStyle:'oblique',
                //字体粗细 'normal','bold','bolder','lighter',100 | 200 | 300 | 400...
                fontWeight:'bold',
                //字体系列
                fontFamily:'FangSong'
            },
            textStyle:{
                //文字颜色
                color:'#f30008',
                //字体风格,'normal','italic','oblique'
                fontStyle:'oblique',
                //字体粗细 'normal','bold','bolder','lighter',100 | 200 | 300 | 400...
                fontWeight:'bold',
                //字体系列
                fontFamily:'FangSong'
                //fontFamily: 'KaiTi'
                //字体大小
                //fontSize:18
            }

            //modiefied end
        },
        tooltip: {
            trigger: 'item',
            formatter: function (params) {      // 鼠标移动到地图  显示： 地点：数值
                return params.name + ' : ' + params.value[2];
            }
        },
        visualMap: {
            min: 0,
            max: max,       //设置热力图的最小最大热力值
            calculable: true,
            inRange: {
                color: ['#50a3ba', '#eac736', '#d94e5d']    //随着热力值变化  颜色的变化
            }
        },
        geo: {
            map: 'china',
            label: {
                emphasis: {
                    show: true
                }
            }
        },
        series: [
            {
                type: 'scatter',
                coordinateSystem: 'geo',    //加载地图参数
                data: convertData(city_data)
            }
        ]
    };
    heatMap.setOption(mapOption);

    //双饼图
    var mothWork = echarts.init(document.getElementById('mothWork'));
    var data_2018 = [{% for r in rs_gmw[0] %}{name:"{{r.month}}", value:"{{r.job_count}}"},{% endfor %}]
    var data_2019 = [{% for r in rs_gmw[1] %}{name:"{{r.month}}", value:"{{r.job_count}}"},{% endfor %}]
    var array_2018 = [{% for r in rs_gmw[0] %}"{{r.job_count}}",{% endfor %}];
    var array_2019 = [{% for r in rs_gmw[1] %}"{{r.job_count}}",{% endfor %}];
    var max_2018 = [];
    var max_2019 = [];
    array_2018.forEach(function(data){
        max_2018.push(+parseInt(data));   ///遍历数组   将每个元素变成整型
    });
    max_2019.forEach(function(data){
        max_2019.push(+parseInt(data));   ///遍历数组   将每个元素变成整型
    });

    console.log('2018年最多职位数量为:' + Math.max.apply(null, max_2018));
    console.log('2018年最多职位数量为:' + Math.max.apply(null, array_2019));

    mothWorkOption = {
        title : {
            text: '大数据"相关职位招聘数量（按月份分组）',
            subtext: '双饼图',
            x:'center',
            //modified 0523
            textStyle:{
                //文字颜色
                color:'#f30008',
                //字体风格,'normal','italic','oblique'
                fontStyle:'oblique',
                //字体粗细 'normal','bold','bolder','lighter',100 | 200 | 300 | 400...
                fontWeight:'bold',
                //字体系列
                fontFamily:'FangSong'
                //fontFamily: 'KaiTi'
                //字体大小
                //fontSize:18
            }
            //modified end
        },
        tooltip : {
            trigger: 'item',
            formatter: "{b}:{c}({d}%)"      //当鼠标移动到图形 显示数据(格式): 佛山（1111） 10%
        },
        legend: {
            x : 'center',
            y : '30%',
            data:['2018年（左图）', '2019年（右图）']
        },
        series : [
            {
                name: '2018年（左图）',
                type:'pie',
                radius : [0, 110],     //图像的大小
                center : ['25%', '50%'],    //图形的位置
                data:data_2018
            },
            {
                name: '2019年（右图）',
                type:'pie',
                radius : [0, 110],
                center : ['75%', '50%'],
                data:data_2019
            }
        ]
    };
    mothWork.setOption(mothWorkOption);

    //词云
    var wordCloud = new Js2WordCloud(document.getElementById('wordCloud'));
    var word_cloud = [{% for r in rs_gsr %}["{{r.skill}}","{{r.frequency}}"],{% endfor %}]

    console.log('词云数据:' + word_cloud);

    wordCloudoPtion = {
        tooltip: {
            show: true
        },
        list: word_cloud,
        color: '#15a4fa',   //字体颜色
        backgroundColor: '#ccc',
        shape: 'circle',
        ellipticity: 1
    };
    wordCloud.setOption(wordCloudoPtion);

</script>

</html>
```



### 4.13 **运行Flask程序**

 (1)分析并统计招聘数量"大数据"相关职位招聘数量,分别使用柱状图表达，同时在网页后台输出相关数据打印语句。

![img](file:///C:\Users\zheng\AppData\Local\Temp\ksohtml16708\wps2.jpg) 

(2）以招聘大数据相关岗位数量为依据绘制散点热度地图。同时在网页后台输出相关数据打印语句

 

![img](file:///C:\Users\zheng\AppData\Local\Temp\ksohtml16708\wps3.jpg) 

（3）请统计2018年，2019年各月份大数据相关职位的招聘数量，以左右分布的（左侧2018年度，右侧2019年度）饼图进行表达。

![img](file:///C:\Users\zheng\AppData\Local\Temp\ksohtml16708\wps4.jpg) 

（4）分析各知识技能在某个招聘岗位能力需求中的占比情况，通过词云图例进行呈现。

![img](file:///C:\Users\zheng\AppData\Local\Temp\ksohtml16708\wps5.jpg) 