Title: MySQL常用
Date: 2016-01-01 13:15
Category: Database
Tags: MySQL
Author: 刘理想

[TOC]

本文将持续更新。

##1. Mac上安装后需要配置的PATH

```
export PATH=$PATH:/usr/local/mysql/bin
```

##2. Python使用MySQL

Python中有5种常见客户端可连接MySQL.

###2.1 `MySQLdb`

最早的，使用C语言写成，但是不支持Python3

###2.2 `mysql-connector-python`

https://pypi.python.org/pypi/mysql-connector-python/

这个是Oracle官方支持的，是纯Python编写的，但是有点慢，而且不兼容`MySQLdb`

###2.3 `pymysql`（推荐）

https://pypi.python.org/pypi/PyMySQL

它是一个纯Python的客户端，并且支持Python3.兼容`MySQLdb`

###2.4 `CyMySQL`

https://github.com/nakagami/CyMySQL

它是`PyMySQL`的一个fork，使用Cython进行了加速，并且支持Python3。

###2.5 `mysqlclient`（Django官方推荐）

https://pypi.python.org/pypi/mysqlclient

`mysqlclient`是`MySQLdb`的一个fork，能够直接代替`MySQLdb`，并且支持Python3，是Django官方推荐的选择。

目前来讲，`pymysql`使用的人最多。

##3. 使用root用户

###3.1 并且修改root密码
```
mysql -u root #进入mysql
#或者已经有密码了
mysql -u root -p 密码
#修改密码
mysql>update mysql.user set password=PASSWORD('123456') where User='root'
```

但是，在MySQL5.6之后，这么更改用户会出错：
```
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement
```

这个时候的做法是：
```
SET PASSWORD = PASSWORD('your new password');
ALTER USER 'root'@'localhost' PASSWORD EXPIRE NEVER;
flush privileges;
```

参考：https://dev.mysql.com/doc/refman/5.6/en/alter-user.html

###3.2 退出

```
exit;
```

