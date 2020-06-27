Title: Django REST Framework笔记-01 介绍与安装
Date: 2014-09-08 22:15
Category: Python
Tags: Python, Django
Author: 刘理想
Summary: Django REST Framework笔记-01 介绍与安装

##一、 Django Rest Framework介绍
项目地址：http://www.django-rest-framework.org/

Django REST framewrok（简称DRF），强大而且灵活的工具箱，它使创建Web API变得简单。

你需要使用REST框架的一些原因：

- 基于Web可浏览的API能让你赢得更多开发者
- 包含OAuth1a和OAuth2的认证机制
- 序列化同时支持ORM和非ORM的数据源
- 自定义，如果不想用更为强大的功能，可以只使用常规的函数视图
- 大量的文档，牛叉的社区支持
- 大公司比如Mozilla和Eventbrite都用它，靠谱~

![Browsable API](http://www.django-rest-framework.org/img/quickstart.png)

##二、安装需求

REST框架具有以下要求：

- Python(2.6.5+, 2.7, 3.2, 3.3)
- Django(1.4.2+, 1.5, 1.6, 1.7)

下面的包是可选的：

- [Markdown](http://pypi.python.org/pypi/Markdown/) (2.1.0+) - 支持可浏览的API
- [PyYAML](http://pypi.python.org/pypi/PyYAML) (3.10+) - 支持YAML的content-type
- [defusedxml](https://pypi.python.org/pypi/defusedxml) (0.3+) - 支持XML的content-type
- [django-filter](http://pypi.python.org/pypi/django-filter) (0.5.4+) - 支持过滤
- [django-oauth-plus](https://bitbucket.org/david/django-oauth-plus/wiki/Home) (2.0+)和[oauth2](https://github.com/simplegeo/python-oauth2) (1.5.211+) - 支持OAuth 1.0a
- [django-oauth2-provider](https://github.com/caffeinehit/django-oauth2-provider) (0.2.3+) - 支持OAuth 2.0
- [django-guardian](https://github.com/lukaszb/django-guardian) (1.1.1+) - 支持对象级别的权限控制

**注意**: `oauth2`包很容易让人误解为OAuth2，其实只支持OAuth 1.0a，并且注意在Python3中OAuth 1.0a和Oauth2.0不能同时存在。

##三、安装

1、使用`pip`进行安装，包含那些你想要的可选包：

```
pip install djangorestframework
pip install markdown       # Markdown support for the browsable API.
pip install django-filter  # Filtering support
```

或者从github上克隆：

```
git clone git@github.com:tomchristie/django-rest-framework.git
```

2、添加`rest_framework`到settings.py的`INSTALLED_APPS`

```python
INSTALLED_APPS = (
    ...
    'rest_framework',
)
```

3、(可选)如果想要增加可浏览的API，你也将需要添加REST框架的login和logout视图。添加如下行到根`urls.py`文件中。

```python
urlpatterns = patterns('',
    ...
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
)
```

**注意**，URL路径可以是任意的。但是必须使用`'rest_framework'`命名空间来包含`'rest_framework.urls'`

##四、例子
来看一个用REST框架建立基于model的简单API例子。

我们将创建一个获取获取工程用户信息的读写API。

所有REST框架的全局设置都被保存到一个叫做`REST_FRAMEWORK`的配置字典中。我们在`settings.py`模块中添加下面的内容：

```python
REST_FRAMEWORK = {
    # 使用Django标准的`django.contrib.auth`权限,
    # 或者对未验证的用户使用只读权限
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.DjangoModelPermissionsOrAnonReadOnly'
    ]
}
```

不要忘了在`INSTALLED_APPS`中添加`rest_framework`。

现在我们已经准备好了创建API了。下面是项目的根`urls.py`模块：

```python
from django.conf.urls import url, include
from django.contrib.auth.models import User
from rest_framework import routers, serializers, viewsets

# Serializers 定义了如何展示API
class UserSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = User
        fields = ('url', 'username', 'email', 'is_staff')

# ViewSets定义了View的行为
class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer

# Routers提供了一种简单的方法来自动决定如何路由
router = routers.DefaultRouter()
router.register(r'users', UserViewSet)

# 使用自动路由来提供API
# 另外，提供了登录查看web API的功能
urlpatterns = [
    url(r'^', include(router.urls)),
    url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework'))
]
```

现在可以在浏览器中输入http://127.0.0.1:8000/来打开API了。并且查看你刚创建的'users' API。如果你在右上角点登录，那么现在就应该可以添加、创建和删除用户了。

##五、快速开始

迫不及待的想要开始了？[快速开始](http://www.django-rest-framework.org/tutorial/quickstart)是最快的方法让你能够创建并运行REST API的方法。

##六、教程

教程将让你通过REST框架创建每一部分。学习它需要一些时间，但是它会让你对每一部分如何相互匹配在一起邮个全方位的了解，强烈建议读一下。

- [1-序列化](http://www.django-rest-framework.org/tutorial/1-serialization)
- [2-请求和相应](http://www.django-rest-framework.org/tutorial/2-requests-and-responses)
- [3-基于类的视图](http://www.django-rest-framework.org/tutorial/3-class-based-views)
- [4-认证与权限](http://www.django-rest-framework.org/tutorial/4-authentication-and-permissions)
- [5-关系和超链接API](http://www.django-rest-framework.org/tutorial/5-relationships-and-hyperlinked-apis)
- [6-视图集合和路由](http://www.django-rest-framework.org/tutorial/6-viewsets-and-routers)

为了测试，我们提供了一个API教程结束时的例子，在[这里](http://restframework.herokuapp.com/).