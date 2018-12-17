# 错误记录
## 1. Command "python setup.py egg_info" failed with error code 1 in C:\Users\Liying     \AppData\Local\Temp\pip-install-erzpci5r\xadmin\

### 解决方法：这个问题可以用源码安装的方式解决：
### Install：
Xadmin is best installed via PyPI. To install the latest version, run:

- pip install xadmin
- Install from github source:
  pip install git+git://github.com/sshwsfc/xadmin.git
- Install from github source for Django 2.0:
  pip install git+git://github.com/sshwsfc/xadmin.git@django2
--------------------------------------------------------------------------------------
## django2.0集成xadmin0.6报错集锦
### 1、django2.0把from django.core.urlresolvers修改成了django.urls
### 报错如下

![11][aa]

[aa]: image/11.png

### 解决方法：
### 修改D:\Envs\django-xadmin\lib\site-packages\xadmin-0.6.1-py3.6.egg\xadmin\models.py 文件
### 把from django.core.urlresolvers import NoReverseMatch, reverse 修改为：

![12][dd]

[dd]: image/12.png

### 2、django2.0中需要给外键ForeignKey指定on_delete参数
### 报错如下

![21][bb]

[bb]: image/21.png

### 解决方法：
### 把content_type = models.ForeignKey(ContentType)修改为：

![22][cc]

[cc]: image/22.png


### 3、 django2.0 forms表单初始化只需要一个参数  
### 报错如下：

![31][ee]

[ee]: image/31.png

### 解决方法：
### 把forms.Field.__init__(self, required, widget, label, initial, help_text, *args, **kwargs) 修改成：

![32][ff]

[ff]: image/32.png

### 4、 导入QUERY_TERMS报错
### 报错如下：

![41][gg]

[gg]: image/41.png

### 解决方法：
### 把  from django.db.models.sql.query import LOOKUP_SEP, QUERY_TERMS
### 修改为：

![42][hh]

[hh]: image/42.png


### 5、Settings缺少MIDDLEWARE_CLASSES属性，django2.0把MIDDLEWARE_ClASSES改成MIDDLEWARE
### 报错如下：

![51][ii]

[ii]: image/51.png

### 把
### if settings.LANGUAGES and 'django.middleware.locale.LocaleMiddleware' in settings.MIDDLEWARE_ClASSES:
### 修改为：

![52][jj]

[jj]: image/52.png

### 6、 django-formtools导入失败，需要更新django-formtools
### 报错如下：

![61][kk]

[kk]: image/61.png

### 卸载django-formtools
### pip uninstall django-formtools
### 重新安装新版本的django-formtools

![62][ll]

[ll]: image/62.png


### cannot import name 'password_reset_confirm'

from django.contrib.auth.views import PasswordResetForm

-------------------------------------------------------------------------------
安装pip的简便方法。

https://bootstrap.pypa.io/get-pip.py 这个里面是安装pip的代码。复制出来直接执行

python get-pip.py

-------------------------------------------------------------------------------
