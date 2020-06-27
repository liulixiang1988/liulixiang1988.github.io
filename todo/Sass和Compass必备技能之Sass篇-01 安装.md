Title: Sass和Compass必备技能之Sass篇-01 安装
Date: 2015-04-16 14:55
Category: Web
Tags: CSS, SASS
Author: 刘理想

##1. ruby安装

RVM: Ruby Version Manager（Ruby多版本管理器）

安装RVM

mac下安装homebrew cakebrew

```
$ gem sources --remove https://rubygems.org/
$ gem sources -a https://ruby.taobao.org/
$ gem sources -l
*** CURRENT SOURCES ***
https://ruby.taobao.org

# 请确保只有 ruby.taobao.org
$ gem install rails #安装
$ gem update #更新
$ gem install sass --version=3.3
$gem uninstall sass
```

##2. 安装sass

```
$gem install sass
$gem install sass --version=3.3
$sass -v
```

##3. 创建sass

```
mkdir learn-sass-cli
cd learn-sas-cli
```
创建main.scss

```
*{
    margin:0;
    padding:0;
}
```

编译
```
sass main.scss main.css
```

多了一个main.css.map文件用来存放映射
.sass-cache是用来存放编译临时文件，不用管它。

##4. 安装和使用compass

```
gem isntall compass
compass create learn-compass-init
#编译的两种方法
compass compile [path/to/project]
compass watch [path/to/project]
```



