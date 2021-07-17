---
title: MySQL优化
date: 2021-07-10 16:54
category: Database
tags: 
- Database
- MySQL
author: Lixiang Liu
---

[TOC]

## 1. Opertimizing Data Access

MySQL提供了Sakila示例数据库供学习

1. 只返回应用需要的**行**：恰当地使用`WHERE`语句
2. 只返回应用需要的**列**：避免使用`SELECT *`，尽量指定列名
3. 避免对同样的数据多次查询：使用应用缓存
4. order the data only if you are not ordering them in application: 使用`ORDER BY`，避免在应用中排序
5. `SELECT DISTINCT name, last_name`去重

## 2. MySQL Query Optimization

查询语句执行时经历的组件：

![mysql-1](http://liulixiang1988.github.io/images/2021-07-10-mysql-1.png)

