---
title: Django SQL Server数据库驱动,根据现有数据库生成models以及数据库迁移
date: 2014-09-26 18:55
category: Python
tags: Python, Django
author: 刘理想
---

## 1. SQL Sever驱动

### 1.1 安装
mssql数据库 使用django-mssql，依赖pywin32，如果安装时发生错误，请用下面这种方法：

    pip install django-mssql --pre 
    
### 1.2 配置
在settings.py中，设置：

    # Database
    # https://docs.djangoproject.com/en/1.6/ref/settings/#databases
    
    DATABASES = {
        'default': {
            'ENGINE': 'sqlserver_ado',
            'NAME': 'data_center',
            'USER': 'sa',
            'PASSWORD': 'test',
            'HOST': 'localhost',
        }
    }

---

## 2. 根据现有数据库生产models

    python manage.py inspectdb > models.py

---

## 3. 数据库迁移

### 3.1 south安装

    pip install south
    ./manage.py syncdb
    
### 3.2 在新建的App中使用South
a. 前期准备
创建信app,并将其添加到INSTALL_APPS中：

    ./manage.py startapp testapp

打开app的models.py，创建model：

b. 第一次迁移

    ./manage.py schemamigration testapp --initial
    ./mange.py migrate testapp
    
c.之后的迁移

    ./manage.py schemamigration testapp --auto
    ./mange.py migrate testapp
    
---

## 4. Django 1.7中的数据库迁移

### 4.1 创建工程之后，进行第一次迁移

当使用`django-admin.py startproject mysite`之后，进行第一次：

    python manage.py migrate
    
### 4.2 之后的迁移分为3部走

- 修改model
- 使用`python manage.py makemigrations 迁移名称`创建迁移
- 使用`python manage.py migrate`应用迁移

**注意**，可以使用`python mange.py sqlmigrate 迁移名 版本号`查看迁移，比如：

    python manage.py sqlmigrate polls 00001
