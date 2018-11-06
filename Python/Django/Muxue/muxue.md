# Django:mxOnline教育平台在线开发
## 一、创建mxonline项目
##### 1. 环境：
(1) 本平台的开发环境
- python2.7.15
- 数据库：mysql
- django：1.9.8
- 在virtualenv中运行
  - `pip install virtualenv`
  - `virtualenv testvir`
  - `pip install virtualenvwrapper-win`
  - `mkvirtualenv mx`
  - `pip install virtualenvwrapper-win`
  - `pip install django==1.9.8`
  - `pip install pillow`
   
(2)虚拟环境原理介绍：

① virtualenv ：
- 安装virtualenv：
  - virtualenv 是用来创建虚拟环境的软件工具，我们可以通过 pip 或者 pip3 来安装：
    `pip install virtualenv`    
    `pip3 install virtualenv`
- 创建虚拟环境
    `virtualenv [虚拟环境的名字]`
- 在不同的操作系统中有不同的方式，一般分为两种，第一种是 Windows ，第二种是 *nix ：
  - windows 进入虚拟环境：进入到虚拟环境的 Scripts 文件夹中，然后执行 activate 。 
  - *nix 进入虚拟环境：`source /path/to/virtualenv/bin/activate` 
  - 一旦你进入到了这个虚拟环境中，你安装包，卸载包都是在这个虚拟环境中，不会影响到外面的环境。
- 退出虚拟环境：deactivate 。
- 创建虚拟环境的时候指定Python解释器,可以通过 -p 参数来指定具体 的 Python 解释器：
    `virtualenv -p C:\Python36\python.exe [virutalenv name]`

② virtualenvwrapper：
- 安装virtualenvwrapper ：
  - `*nix： pip install virtualenvwrapper` 
  - `windows： pip install virtualenvwrapper-win`
- virtualenvwrapper 基本使用：
- 创建虚拟环境：
  `mkvirtualenv my_env`
- 切换到某个虚拟环境：
  `workon my_env`
- 退出当前虚拟环境：
  `deactivate`
- 删除某个虚拟环境：
  `rmvirtualenv my_env`
- 列出所有虚拟环境：
  `lsvirtualenv`
-  进入到虚拟环境所在的目录：
  `cdvirtualenv`
- 修改`mkvirtualenv`的默认路径：
在 我的电脑->右键->属性->高级系统设置->环境变量->系统变量 中添加一个参数 WORKON_HOME ，将这个 参数的值设置为你需要的路径。
- 创建虚拟环境的时候指定 Python 版本：
   `mkvirtualenv --python==C:\Python36\python.exe hy_env`

##### 2. 创建数据库
- create database mxonline;

##### 3. 在pycharm中创建Django项目
- 修改__init__.py文件
```
import pymysql
pymysql.install_as_MySQLdb()
```
- 安装pymysql:`pip install pymysql`
- 修改settings.py文件
  - 配置数据库
	```
	DATABASES = {
	    'default': {
	        'ENGINE': 'django.db.backends.mysql',
	        'NAME': 'mxonline',
	        'USER': 'root',
	        'PASSWORD': '',
	        'HOST': 'localhost',
	        'port': '3306',
	    }
	}
	```
  - 汉化
	```
	LANGUAGE_CODE = 'zh-hans'
	TIME_ZONE = 'Asia/Shanghai'
	USE_I18N = True
	USE_L10N = True
	#防止取为UTC时间
	USE_TZ = False
	```

## 二、创建应用
- 在一个项目中可以创建多个应用，每个应用对应一种业务处理
- 点击Tools下的run manage.py task
- 执行`<startapp myApp>`
  - startapp users
  - startapp courses
  - startapp organization
  - startapp operation
- myApp目录说明	
  - admin.py　　　　站点配置
  - model.py　　　　模型
  - views.py　　　　视图

## 三、激活应用
- 在setting.py文件中，将myApp应用加入到INSTALLED_APPS选项中
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'users',
    'courses',
    'operation',
    'organization',
]
```

## 四、定义模型
##### 1. 概述：一个数据表对应一个模型
##### 2. 在models.py文件中定义模型
　　　　　　　　　　　　　　　　![](img/mx41.png)

(1)users  
```
# _*_ encoding:utf-8 _*_
from __future__ import unicode_literals
from datetime import datetime
from django.db import models
from django.contrib.auth.models import AbstractUser

# Create your models here.


class UserProfile(AbstractUser):
    nick_name = models.CharField(max_length=50, verbose_name=u"昵称", default=u"")
    birthday = models.DateField(verbose_name=u"生日", null=True, blank=True)
    gender = models.CharField(max_length=10, choices=(("male", u"男"), ("female", u"女")), default="female")
    address = models.CharField(max_length=100, default=u"")
    mobile = models.CharField(max_length=11, null=True, blank=True)
    image = models.ImageField(upload_to="image/%Y/%X", default=u"image/default.png")

	class Meta:
	    verbose_name = "用户信息"
	    verbose_name_plural = verbose_name


class EmailVerifyRecord(models.Model):
    code = models.CharField(max_length=20,verbose_name=u"验证码")
    email = models.EmailField(max_length=50, verbose_name=u"邮箱")
    send_type = models.CharField(max_length=20, choices=(("register",u"注册"), ("forget", u"忘记密码"), ("found", u"找回密码")))
    # 去掉datetime.now()的(),使生成时间是实例化时间，而不是模型建立时间
    send_time = models.DateTimeField(default=datetime.now)

    class Meta:
        verbose_name = "邮箱验证码"
        verbose_name_plural = verbose_name

    def __unicode__(self):
        return self.username


class Banner(models.Model):
    title = models.CharField(max_length=100, verbose_name=u"标题")
    image = models.ImageField(upload_to="banner/%Y/%m", verbose_name=u"轮播图", max_length=100)
    url = models.URLField(max_length=200, verbose_name=u"访问地址")
    index = models.IntegerField(default=100, verbose_name=u"顺序")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"轮播图"
        verbose_name_plural = verbose_name
```
- 由于UserProfile继承了AbstractUser，因此需要在settings.py中添加
  `AUTH_USER_MODEL = "users.UserProfile"`
- 否则会出现下面的错误
```
auth.User.groups: (fields.E304)
auth.User.user_permissions: (fields.E304)
users.UserProfile.groups: (fields.E304) 
users.UserProfile.user_permissions: (fields.E304)
```

(2)courses
```
# _*_ encoding:utf-8 _*_
from __future__ import unicode_literals
from django.db import models
from datetime import datetime

# Create your models here.


class Course(models.Model):
    name = models.CharField(max_length=50, verbose_name=u"课程名")
    desc = models.CharField(max_length=300, verbose_name=u"课程描述")
    detail = models.TextField(verbose_name=u"课程详情")
    degree = models.CharField(max_length=50, choices=(("cj", "初级"), ("zj", "中级"), ("gj", "高级")))
    learn_times = models.IntegerField(default=0, verbose_name=u"学习时长(分钟数)")
    students = models.IntegerField(default=0, verbose_name=u"学习人数")
    fav_nums = models.IntegerField(default=0, verbose_name=u"收藏人数")
    image = models.ImageField(upload_to="Courses/%Y/%m", verbose_name=u"封面图片")
    click_nums = models.IntegerField(default=0, verbose_name=u"点击数")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"课程"
        verbose_name_plural = verbose_name


class Lesson(models.Model):
    course = models.ForeignKey(Course, verbose_name=u"课程",on_delete=models.DO_NOTHING)
    name = models.CharField(max_length=100, verbose_name=u"章节名")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"章节"
        verbose_name_plural = verbose_name


class Video(models.Model):
    lesson = models.ForeignKey(Lesson, verbose_name=u"章节",on_delete=models.DO_NOTHING)
    name = models.CharField(max_length=100, verbose_name=u"视频名")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"视频"
        verbose_name_plural = verbose_name


class CourseResource(models.Model):
    course = models.ForeignKey(Course, verbose_name=u"课程",on_delete=models.DO_NOTHING)
    name = models.CharField(max_length=100, verbose_name=u"名称")
    download = models.FileField(upload_to="course/resource/%Y/%m", verbose_name=u"资源文件")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"课程资源"
        verbose_name_plural = verbose_name
```
(3) operation
```
# _*_ encoding:utf-8 _*_
from __future__ import unicode_literals

from datetime import datetime

from django.db import models

from users.models import UserProfile
from courses.models import Course

# Create your models here.


class UserAsk(models.Model):
    name = models.CharField(max_length=20, verbose_name=u"姓名")
    mobile = models.CharField(max_length=11, verbose_name=u"手机")
    course_name = models.CharField(max_length=50, verbose_name=u"课程名")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"用户咨询"
        verbose_name_plural = verbose_name


class CourseComments(models.Model):
    user = models.ForeignKey(UserProfile, verbose_name=u"用户", on_delete=models.DO_NOTHING)
    course = models.ForeignKey(Course, verbose_name=u"课程", on_delete=models.DO_NOTHING)
    comments = models.CharField(max_length=200, verbose_name=u"评论")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"课程评论"
        verbose_name_plural = verbose_name


class UserFavorite(models.Model):
    user = models.ForeignKey(UserProfile, verbose_name=u"用户",on_delete=models.DO_NOTHING)
    fav_id = models.IntegerField(default=0, verbose_name=u"数据id")
    fav_type = models.IntegerField(choices=((1, "课程"), (2, "课程机构"), (3, "讲师")))
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"用户收藏"
        verbose_name_plural = verbose_name


class UserMessage(models.Model):
    user = models.IntegerField(default=0, verbose_name=u"接收用户")
    message = models.CharField(max_length=500, verbose_name=u"消息内容")
    has_read = models.BooleanField(default=False, verbose_name=u"是否已读")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"用户消息"
        verbose_name_plural = verbose_name


class UserCourse(models.Model):
    user = models.ForeignKey(UserProfile, verbose_name=u"用户",on_delete=models.DO_NOTHING)
    course = models.ForeignKey(Course, verbose_name=u"课程",on_delete=models.DO_NOTHING)
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"用户课程"
        verbose_name_plural = verbose_name
```
(4)organization
```
# _*_ encoding:utf-8 _*_
from __future__ import unicode_literals
from datetime import datetime

from django.db import models

# Create your models here.


class CityDict(models.Model):
    name = models.CharField(max_length=50, verbose_name=u"城市")
    desc = models.CharField(max_length=300, verbose_name=u"描述")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"城市"
        verbose_name_plural = verbose_name


class CourseOrg(models.Model):
    name = models.CharField(max_length=50, verbose_name=u"机构名称")
    desc = models.CharField(max_length=300, verbose_name=u"机构描述")
    click_nums = models.IntegerField(default=0, verbose_name=u"点击数")
    fav_nums = models.IntegerField(default=0, verbose_name=u"收藏人数")
    image = models.ImageField(upload_to="org/%Y/%m", verbose_name=u"封面图片")
    address = models.CharField(max_length=50, verbose_name=u"机构地址")
    city = models.ForeignKey(CityDict,verbose_name=u"所在城市",on_delete=models.DO_NOTHING)
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"课程机构"
        verbose_name_plural = verbose_name


class Teacher(models.Model):
    org = models.ForeignKey(CourseOrg, verbose_name=u"所属机构",on_delete=models.DO_NOTHING)
    name = models.CharField(max_length=50,verbose_name=u"教师名")
    work_years = models.IntegerField(default=0, verbose_name=u"工作年限")
    work_company = models.CharField(max_length=50, verbose_name=u"就职公司")
    work_position = models.CharField(max_length=50, verbose_name=u"公司职位")
    points = models.CharField(max_length=50, verbose_name=u"教学特点")
    click_nums = models.IntegerField(default=0, verbose_name=u"点击数")
    fav_nums = models.IntegerField(default=0, verbose_name=u"收藏人数")
    add_time = models.DateTimeField(default=datetime.now, verbose_name=u"添加时间")

    class Meta:
        verbose_name = u"教师"
        verbose_name_plural = verbose_name
```
- 说明：不需要定义主键，在生成时自动添加，并且值为自动增加

## 五、在数据库中生成数据表
- 生成迁移文件
  - 执行`<makemigrations>`
    - 在migrations目录下生成一个迁移文件，此时数据库中还没有生成数据表
- 执行迁移
	- 执行`<migrate>`
	- 相当于执行sql语句创建数据表
	
　　　　　　　　　　　　　　　　　　　![](img/mx51.png)

## 六、创建python package<apps>,用于放置所有app
- 将创建的apps文件夹mark为root
  - 此时编辑器知道，命令行不知道
- 将apps加入到python的搜索目录之下
```
import sys
sys.path.insert(0, os.path.join(BASE_DIR, 'apps'))
```

## 七、测试
- 创建超级用户
  - 执行`<createsuperuser>`
    - username
    - email
    - password
- 启动服务器
  - 格式
	  - python manage.py runserver ip:port
	  - ip可以不写，代表本机ip
	  - 端口号默认是8000
- 说明
  - 这是一个纯python写的轻量级web服务器，仅仅在开发测试中使用

## 八、站点管理
##### 1. Admin
- 自定义管理页面
```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals
from django.contrib import admin
# Register your models here.
from .models import UserProfile
class UserProfileAdmin(admin.ModelAdmin):
    pass
admin.site.register(UserProfile, UserProfileAdmin)
```

##### 2.xadmin
(1) 安装xadmin
- [克隆](https://github.com/sshwsfc/xadmin.git)
- 将文件夹中的xadmin文件夹放在项目目录下
- 新建extra_apps文件夹，将xadmin放入该目录下
	- mark 为source root
	- 添加settings.py文件
	  - `sys.path.insert(0, os.path.join(BASE_DIR, 'extra_apps'))`
	  
(2) 注册站点
- INSTALLED_APPS添加
```
  'xadmin',
  'crispy_forms'
```
- 添加`httplib2`,`django-formtools`,`django-crispy-forms`,`future`,`django-import-export`等库.
- 需要完成迁移生成xadmin表才能打开网址

　　　　　　　　　　　　　　　　　　　　![](img/mx81.png)
- urls.py文件下

	```
	from django.conf.urls import url
	from django.contrib import admin
	import xadmin
	
	urlpatterns = [
	    url(r'^xadmin/', xadmin.site.urls),
	]
	```
- 在settings中设置template   
	         
	```
	- # -*- coding:utf-8 -*-
	__author__ = 'liying'
	__data__= '$DATE $TIME'
	```

- ######  在每一个app下建立adminx.py文件:
- users/adminx.py

```
# -*- coding:utf-8 -*-
__author__ = 'liying'
__data__= '2018/09/27 15:36'

import xadmin

from .models import EmailVerifyRecord, Banner


class EmailVerifyRecordAdmin(object):
    list_display = ['code', 'email', 'send_type', 'send_time']
    search_fields = ['code', 'email', 'send_type']
    list_filter = ['code', 'email', 'send_type', 'send_time']


class BannerAdmin(object):
    list_display = ['title', 'image', 'url', 'index', 'add_time']
    search_fields = ['title', 'image', 'url', 'index']
    list_filter = ['title', 'image', 'url', 'index', 'add_time']


xadmin.site.register(EmailVerifyRecord, EmailVerifyRecordAdmin)
xadmin.site.register(Banner, BannerAdmin)
```
- courses/adminx.py
   
```
# -*- coding:utf-8 -*-
__author__ = 'liying'
__data__ = '2018/9/27 16:04'

import xadmin
from .models import Course, Lesson, Video, CourseResource


class CourseAdmin(object):
    list_display = ['name', 'desc', 'detail', 'degree', 'learn_times', 'students']
    search_fields = ['name', 'desc', 'detail', 'degree', 'students']
    list_filter = ['name', 'desc', 'detail', 'degree', 'learn_times', 'students']


class LessonAdmin(object):
    list_display = ['course', 'name',  'add_time']
    search_fields = ['course', 'name']
    list_filter = ['course__name', 'name',  'add_time']


class VideoAdmin(object):
    list_display = ['lesson', 'name',  'add_time']
    search_fields = ['lesson', 'name']
    list_filter = ['lesson', 'name',  'add_time']


class CourseResourceAdmin(object):
    list_display = ['course', 'name', 'download', 'add_time']
    search_fields = ['course', 'name', 'download']
    list_filter = ['course', 'name', 'download', 'add_time']


xadmin.site.register(Course, CourseAdmin)
xadmin.site.register(Lesson, LessonAdmin)
xadmin.site.register(Video, VideoAdmin)
xadmin.site.register(CourseResource, CourseResourceAdmin)
```
- operation/adminx.py

```
# -*- coding:utf-8 -*-
__author__ = 'liying'
__data__ = '2018/9/27 16:32'

import xadmin

from .models import UserAsk, UserCourse, UserMessage, CourseComments, UserFavorite


class UserAskAdmin(object):
    list_display = ['name', 'mobile', 'course_name', 'add_time']
    search_fields = ['name', 'mobile', 'course_name']
    list_filter = ['name', 'mobile', 'course_name', 'add_time']


class UserCourseAdmin(object):
    list_display = ['user', 'course', 'add_time']
    search_fields = ['user', 'course']
    list_filter = ['user', 'course', 'add_time']


class UserMessageAdmin(object):
    list_display = ['user', 'message', 'has_read', 'add_time']
    search_fields = ['user', 'message', 'has_read']
    list_filter = ['user', 'message', 'has_read', 'add_time']


class CourseCommentsAdmin(object):
    list_display = ['user', 'course', 'comments', 'add_time']
    search_fields = ['user', 'course', 'comments', 'add_time']
    list_filter = ['user', 'course', 'comments', 'add_time']


class UserFavoriteAdmin(object):
    list_display = ['user', 'fav_id', 'fav_type', 'add_time']
    search_fields = ['user', 'fav_id', 'fav_type']
    list_filter = ['user', 'fav_id', 'fav_type', 'add_time']


xadmin.site.register(UserAsk, UserAskAdmin)
xadmin.site.register(UserCourse, UserCourseAdmin)
xadmin.site.register(UserMessage, UserMessageAdmin)
xadmin.site.register(CourseComments, CourseCommentsAdmin)
xadmin.site.register(UserFavorite, UserFavoriteAdmin)
```
- organization/adminx.py

```
# -*- coding:utf-8 -*-
__author__ = 'liying'
__data__ = '2018/9/27 16:25'

import xadmin


from .models import CityDict,CourseOrg,Teacher


class CityDictAdmin(object):
    list_display = ['name', 'desc', 'add_time']
    search_fields = ['name', 'desc']
    list_filter = ['name', 'desc', 'add_time']


class CourseOrgAdmin(object):
    list_display = ['name', 'desc', 'click_nums', 'fav_nums']
    search_fields = ['name', 'desc', 'click_nums', 'fav_nums']
    list_filter = ['name', 'desc', 'click_nums', 'fav_nums']


class TeacherAdmin(object):
    list_display = ['org', 'name', 'work_years', 'work_company']
    search_fields = ['org', 'name', 'work_years', 'work_company']
    list_filter = ['org', 'name', 'work_years', 'work_company']


xadmin.site.register(CityDict, CityDictAdmin)
xadmin.site.register(CourseOrg, CourseOrgAdmin)
xadmin.site.register(Teacher, TeacherAdmin)

```

(3) 应用主题
- users/adminx.py
```
from xadmin import views
class BaseSetting(object):
  enable_themes = True
  use_bootswatch = True
```

(4) 显示样式
- users/adminx.py
```
class GlobalSettings(object):
  site_title = "慕学后台管理系统"
  site_footer = "慕学在线网"
```

（5）形成折叠目录
- users/adminx.py
```
class GlobalSettings(object):
    menu_style = "accordion"
```

(6) 对app名字进行修改（以users为例）
- users/apps.py
```
# -*- coding: utf-8 -*-
from __future__ import unicode_literals
from django.apps import AppConfig
class UsersConfig(AppConfig):
    name = 'users'
    verbose_name = u"用户信息"
```
- users/__init__.py
`default_app_config = "users.apps.UsersConfig"`

(7)注册
```
xadmin.site.register(views.BaseAdminView, BaseSetting)
xadmin.site.register(views.CommAdminView, GlobalSettings)
```
------------------------------------------------------------------------------------
# 前端
## 九、添加静态文件
