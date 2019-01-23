---
layout: post
title: 在Eclipse中配置Tomcat
date: 2019-1-23
categories: tomcat
tags: [javaStudy]
description: 在Eclipse中配置Tomcat
---

* 目录
{:toc}


在Eclipse中配置Tomcat
=====================

1、在Eclipse菜单中找到window，点击在列表中选择preferences；
![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image001.jpg?raw=true)

2、接着在弹出的窗口中依次点击 Server Runtime Evironments
Add，添加一个服务器到eclipse中；

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image002.jpg?raw=true)

3、然后在下面的窗口中选择 Apache apache tomcat
v7.0，接着点击next下一步；

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image003.jpg?raw=true)

4、在下一个出现的窗口中，点击 Browse
按钮，选择之前自己所安装的tomcat的根路径，如：
D:\\software\\tomcat7.0（注意自己安装的tomcat版本要和上面选择的版本保持一致）。在下一行中选择JDK的版本，这里我们选择自己安装的
JDK1.7(或JDK1.8)；

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image004.jpg?raw=true)

最后点击finish完成配置。

以上是将tomcat服务器整合到eclipse中的整个过程图解！

可能出现的问题
==============

问题描述1：在配置tomcat之前，如果在动态web工程中创建了servlet，servlet中的代码会出现红线及错误提示，如下：

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image005.jpg?raw=true)

原因分析：由于 servlet属于
JavaEE规范中的技术，其中所用到的一些相关的类（如：HttpServlet、HttpServletRequest等）也属于JavaEE规范中的技术，这里实际是缺少了Servlet的依赖包。

解决方案：这个包我们不需要单独去下载，因为tomcat是一个Servlet容器，其中就包含了Servlet的依赖包。如果还没有在eclipse中配置tomcat服务器，请按照上面的方式进行配置。

如果在eclipse中已经配置了tomcat，只需要在当前工程中引入即可！

引入具体步骤为：

1、选中工程，点击右键 Build Path Configure Build Path...，如下图所示：

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image006.jpg?raw=true)

2、在接下来的窗口中，选择 Libraries Add Ligrary...，如下图所示：

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image007.jpg?raw=true)

3、在弹出的窗口中，选择 Server Runtime next，如下图所示：

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image008.jpg?raw=true)

4、在下一个窗口中，选择 前面我们配置的 Apache tomcat v7.0 的tomcat服务器
finish 完成返回到第一个窗口，点击OK即可，如下图所示：

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image009.jpg?raw=true)

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image010.jpg?raw=true)

问题描述2：如果之前配置完tomcat后，启动服务器也没有报错，将WEB应用部署到工程中，启动浏览器访问，却访问不到，显示无法访问，这是由于tomcat配置的过程中某处出现了问题，可以将工程和tomcat服务器从eclipse中彻底删除，再次重新配置即可！

解决方式：（1）删除工程：直接选中工程，右键点击删除即可！

（2）删除在eclipse中配置的tomcat：点击window
-->preferences出现如下窗口

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/eclipse-tomcat.files/image011.jpg?raw=true)
