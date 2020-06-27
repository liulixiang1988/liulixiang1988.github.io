Title: Windows下使用Apache+mod_wsgi部署Django
Date: 2015-06-16 10:22
Category: Python
Tags: python, Django, 部署
Author: 刘理想

这个总结是在[原来笔记](http://www.cnblogs.com/liulixiang/p/3543021.html)里修改而来。

##1、环境

- django 1.8.2
- python 3.4 32位
- apache 2.4.12 32位
- 一个可以使用的django project( https://github.com/liulixiang1988/python_study_case/tree/master/django)

##2、安装apache 32位
可以在这里下载（http://www.apachelounge.com/download/）
*注意，我们必须在这个网站上下载apache才能运行后面的mod_wsgi*
这时，需要注意我们还必须下载对应的分发环境，如果下载的是apache 2.4 vc14 windows binaries，则必须下载VC14 vcredist_x64/86.exe 

更改基本配置:
打开apache/conf/http.conf文件
- 首先找到`Listen 8080`, 更改为需要的端口
- 找到ServerName，配置域名或者IP: `ServerName 172.16.222.45:8008`
- 找到ServerRoot，更改为apache目录`ServerRoot "d:/Apache24"`
- 找到DocumentRoot，更改为apache对应目录：`DocumentRoot "d:/Apache24/htdocs"<Directory "d:/Apache24/htdocs">`

##3、安装mod_wsgi
注意，因为apache是32位的，所以mod_wsgi也应该选择32位的。mod_wsgi的官网在[这里](https://code.google.com/p/modwsgi/)。但是因为没有windows编译版本（自己编译几乎都是以失败告终），所幸在[github](https://github.com/GrahamDumpleton/mod_wsgi/releases)上可以下载到编译版本，我们选择[mod_wsgi-windows-4.4.12.tar.gz](https://github.com/GrahamDumpleton/mod_wsgi/releases/download/4.4.12/mod_wsgi-windows-4.4.12.tar.gz)进行下载。
下载过mod_wsgi之后，解压之后的文件为“mod_wsgi.so”文件。我们拷贝它到apache安装目录下面的modules文件夹。
光拷贝mod_wsgi.so文件是不行的，我们还需要修改apache的配置文件，让apache知道mod_wsgi的存在。apache的配置文件在apache安装目录下的conf文件夹中的httpd.conf文件。使用记事本或者任何你喜欢的文本编辑器打开它，在文件的最后添加下面的内容并保存就可以了：

```
LoadModule wsgi_module modules/mod_wsgi/mod_wsgi.so
```

##4、配置django project
第一步，你要有个django project，如果你这是想尝试一下如何部署而没有项目的话，可以在到我的github上下载一个https://github.com/liulixiang1988/python_study_case

第二步 修改wsgi.py文件
*这是原来笔记中记录的，在这次的部署中，我发现不需
要这一步*
原以为wsgi.py不需要修改的，可是发现如果不将目录所在的文件夹添加到系统路径中，apache还是会报错，因此在wsgi.py添加下面2行

```
import sys
sys.path.append('E:/git/python_dev/python_study_case/django/mydjangosite') 
```

其中append的参数就是你的project下的目录

第三步 配置apache/conf/httpd.conf文件
打开httpd.conf文件，在后面添加下面内容

```
#添加映射，其中WSGIScriptAlias /是映射到根目录，如果你需要映射到其他地址，可以改为WSGIScriptAlias /myproject （假设myproject是你想要的地址，那么你可以通过访问localhost/myproject来访问你的网站）
WSGIScriptAlias / E:/git/python_dev/python_study_case/django/mydjangosite/mydjangosite/wsgi.py
#指明应用路径，如果你是使用virtualenv，那么还需要添加virtualenv的site-packages路径，中间用":"分隔
WSGIPythonPath E:/git/python_dev/python_study_case/django/mydjangosite:E:/git/python_dev/envs/django/Lib/site-packages
#添加wsgi的读取权限
<Directory E:/git/python_dev/python_study_case/django/mydjangosite/mydjangosite>
     <Files wsgi.py>
          Order deny,allow
          Require all granted
     </Files>
</Directory>

#这里声明静态文静的映射，这个/static是给django admin的静态文件使用的
Alias /static "C:/Python33/Lib/site-packages/django/contrib/admin/static"
#这里是添加文件夹的读取权限，注意apache2.3以上的读写设置与2.3以下版本有所不同
<Directory "C:/Python33/Lib/site-packages/django/contrib/admin/static/"> 
    <IfVersion < 2.3 >
        Order allow,deny
        Allow from all
    </IfVersion>
    <IfVersion >= 2.3>
        Require all granted
    </IfVersion> 
</Directory>
#媒体文件
Alias /media "F:/git/python/app-host/media"
<Directory "F:/git/python/app-host/media"> 
    Require all granted
</Directory>
```

##5、启动apache
打开cmd，定位到apache安装目录下面的bin目录，输入httpd就可以启动服务器了，此时你就可以在你的浏览器中访问localhost了

##6、需要改进的地方
因为工作中使用的是asp.net，对apache不熟，所以对虚拟主机的配置以及mod_wsgi的运行模式的理解还不是特别透彻，也是下一步需要学习的方向。

##7、致谢与参考
首先感谢那些为提供这些优秀开源工程的程序员们；其次在学习如何部署的过程中，下面两篇文章对我的帮助很大，在这里再次说声谢谢!
https://docs.djangoproject.com/en/dev/howto/deployment/wsgi/modwsgi/
http://agong.org/2013/windows64-python3-apache-wsgi-django.html

##8、最后
如果你觉得这篇文章对你比较有帮助，或者有什么问题，欢迎给我留言或者email:550488300@qq.com

