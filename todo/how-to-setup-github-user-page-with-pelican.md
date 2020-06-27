Title: 使用Pelican搭建博客
Date: 2014-08-26 17:08
Category: Python
Tags: Pelican
Author: 刘理想
Summary: 使用Pelican搭建博客


##1 环境配置

###1.1 安装Pelican

**注**:以下是在windows环境下安装pelican，Python版本使用的是3.41版本，该版本自带pip工具

```bash
pip install virtualenv
virtualenv pelican_env
cd pelican_env\script
.\activate.bat
```
上面的命令创建并激活了pelican_env，下面在该环境下开始安装pelican工具

```bash
pip install pelican markdown ghp-import
```

###1.2 创建Github Page

在Github上创建一个仓库，仓库名是`用户名.github.io`，注意这里的用户名是自己的用户名。如下图所示.

![Github Page](images/github.png)

创建好后，克隆到本地

```bash
git clone https://github.com/liulixiang1988/liulixiang1988.github.io
cd liulixiang1988.github.io
```

##2 使用Pelican写博客

###2.1 创建Pelican博客

创建分支

```bash 
git checkout -b source 
```

创建Pelican

```bash
pelican-quickstart
```
创建的过程中，会问你一些问题，你可以参考下面的回答。

```bash
> Where do you want to create your new web site? [.] 
> What will be the title of this web site? Buttermilch
> Who will be the author of this web site? Tony Stark
> What will be the default language of this web site? [en] 
> Do you want to specify a URL prefix? e.g., http://example.com   (Y/n) n
> Do you want to enable article pagination? (Y/n) 
> How many articles per page do you want? [10] 
> Do you want to generate a Fabfile/Makefile ... and publishing? (Y/n) 
> Do you want an auto-reload & simpleHTTP ... and site development? (Y/n) 
> Do you want to upload your website using FTP? (y/N) 
> Do you want to upload your website using SSH? (y/N) 
> Do you want to upload your website using Dropbox? (y/N) 
> Do you want to upload your website using S3? (y/N) 
> Do you want to upload your website using Rackspace Cloud Files? (y/N)
```

###2.2 创建Markdown的博文
在content目录下创建一个test.md的文件，然后用记事本或者notepad++或者sublime text打开，编写如下内容。

```markdown
Title: 你好，世界！
Date: 2014-08-22 16:08
Category: Python
Tags: python
Author: 刘理想
Summary: 你好，世界！
你好，世界，世界，你好。
```

###2.3 本地测试
编写好了之后，就可以在本地进行测试了。

```bash
make html
make serve
```
###2.4 提交更改

```bash
git add .
git commit -m "initial commit"
```

###2.5 发布

```bash
git branch gh-pages
ghp-import output
git checkout master
git merge gh-pages
git push --all
```
##参考

- http://ntanjerome.org/blog/how-to-setup-github-user-page-with-pelican
