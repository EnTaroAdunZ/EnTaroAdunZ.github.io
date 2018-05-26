---
layout: post
title: "基于fxml+hsqldb+jdbc+poi的学生管理demo"
date: 2017-11-23 10:31:55 +0800
comments: true
categories:
tags: [数据库,hsqldb,java]
keyword: 陈浩翔, 谙忆
description:
---


程序说明本程序中界面采用JAVAFX(FXML)编写，数据暂存于内存中(使用JDBC以及JAVA内置的数据库hsqldb)，有初始数据，支持xls导入(使用阿帕奇的POI，运用了与WEB MVC模型进行编写，由于缺少数据校验，xls以及添加的数据需要严格遵守格式(成绩、学号必须为整数，课程以及姓名必须包含汉字)。项目结构运行截图 

导入数据 

[项目地址]https://github.com
<!-- more -->
----------

### 程序说明
**本程序中界面采用JAVAFX(FXML)编写，数据暂存于内存中(使用JDBC以及JAVA内置的数据库hsqldb)，有初始数据，支持xls导入(使用阿帕奇的POI，运用了与WEB MVC模型进行编写，由于缺少数据校验，xls以及添加的数据需要严格遵守格式(成绩、学号必须为整数，课程以及姓名必须包含汉字)。**
### 项目结构
![](http://othgjp7hs.bkt.clouddn.com/17-11-13/73568901.jpg)
### 运行截图
![](http://othgjp7hs.bkt.clouddn.com/17-11-13/65348674.jpg)
![](http://othgjp7hs.bkt.clouddn.com/17-11-13/27331363.jpg)
![](http://othgjp7hs.bkt.clouddn.com/17-11-13/72909458.jpg)
### 导入数据
![](http://othgjp7hs.bkt.clouddn.com/17-11-13/92038342.jpg)
![](http://othgjp7hs.bkt.clouddn.com/17-11-13/73760365.jpg)
[项目地址]https://github.com/EnTaroAdunZ/StudentManagementSystem.git)
