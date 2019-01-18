---
layout: post
title: WebServer
date: 2019-1-16
categories: WebServer
tags: [javaStudy]
description: 手写WebServer

---

* 目录
{:toc}



WebServer 
=========

项目概述
========

项目流程图
----------
![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image003.png?raw=true)

http请求
--------
![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image004.png?raw=true)


http响应
--------
![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image005.png?raw=true)


项目结构
========

项目需求
--------

模拟tomcat服务器，实现接收客户端的请求，经过服务器处理业务，最终把数据响应给客户端、


项目结构
--------

Cn.tedu.core --- 核心代码包

-   WebServer类

-   ClientHandler类

Cn.tedu.http --- http协议相关的代码

-   httpRequest类，封装请求数据

-   HttpResponse类，封装响应数据

Cn.tedu.common --- 全局的配置信息

-   httpContext类，封装http协议的相关配置

-   serverContext 类，封装服务器的相关配置

config 目录 --- 配置信息

-   web.xml

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image006.png?raw=true)

代码实现V1.0 -- 基本实现
=========================

创建WebServer类
---------------

### 声明ServerSocket对象

### 在构造函数中完成ServerSocket对象的初始化

### 提供start方法，接收客户端的请求并响应

### 提供main方法，启动服务器

```java
package cn.tedu.core;

import java.io.IOException;

import java.io.OutputStream;

import java.net.ServerSocket;

import java.net.Socket;

/**

这个类用来代表服务器端的程序

4.1.1 声明ServerSocket对象

4.1.2 在构造函数中完成ServerSocket对象的初始化

4.1.3 提供start方法，接收客户端的请求并响应

4.1.4 提供main方法，启动服务器

*/

public class WebServer {

//1,声明ServerSocket对象

private ServerSocket server;

//2,在构造函数中完成ServerSocket对象的初始化

public WebServer(){

try {

//绑定端口号

server = new ServerSocket(8080);

System.out.println("start server...");

} catch (IOException e) {

e.printStackTrace();

}

}

//3,提供start方法，接收客户端的请求并响应

public void start(){

try {

while(true){

//持续接收客户端请求

Socket socket = server.accept();

//向浏览器输出一句话hello

OutputStream out = socket.getOutputStream();

out.write("hello".getBytes());

out.flush();

}

} catch (IOException e) {

e.printStackTrace();

}

}

//4,提供main方法，启动服务器

public static void main(String[] args) {

WebServer server = new WebServer();

//接收客户端请求，并响应

server.start();

}

}
```

### 测试

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image007.png?raw=true)

### 解决方案

,提供start方法，接收客户端的请求并响应

```java
public void start(){

try {

while(true){

//持续接收客户端请求

Socket socket = server.accept();

//向浏览器输出一句话hello

//组织的数据格式不符合[http]协议

//OutputStream out = socket.getOutputStream();

//out.write("hello".getBytes());

//out.flush();

//改造输出数据的格式

PrintStream ps =new PrintStream(socket.getOutputStream());

//拼接状态行

ps.println("HTTP/1.1 200 OK");

//拼接响应头，响应内容：网页类型

ps.println("Content-Type:text/html");

//拼接响应头，响应内容的长度

String data = "hello server~";

ps.println("Content-Length:"+data.length());

//拼接空白行

ps.println();

//拼接响应实体内容（真正要显示的内容）

ps.write(data.getBytes());

ps.flush();

socket.close();

}

} catch (IOException e) {

e.printStackTrace();

}

}
```

创建ClientHandler类，线程类
---------------------------

抽取start方法中的响应代码

### 声明Socket对象

### 在构造函数中传入socket对象并保存类中

### 重写run方法


```java
package cn.tedu.core;

import java.io.IOException;

import java.io.PrintStream;

import java.net.Socket;

/**

这个类用来作为线程类，抽取start方法的代码

4.2.1 声明Socket对象

4.2.2 在构造函数中传入socket对象并保存类中

4.2.3 重写run方法

*/

public class ClientHandler implements Runnable{

//1,声明Socket对象

private Socket socket;

//2,在构造函数中传入socket对象并保存类中

public ClientHandler(Socket socket){

this.socket=socket;

}

//3,重写run方法，抽取start方法

@Override

public void run() {

try {

//抽取响应代码

PrintStream ps =new PrintStream(socket.getOutputStream());

//拼接状态行

ps.println("HTTP/1.1 200 OK");

//拼接响应头，响应类型：网页

ps.println("Content-Type:text/html");

//拼接响应头，响应数据的长度

String data = "hello server~";

ps.println("Content-Length:"+data.length());

//空白行

ps.println();

//真要显示的数据

ps.write(data.getBytes());

ps.flush();

socket.close();

} catch (IOException e) {

e.printStackTrace();

}

}

}
```

改造WebServer类
---------------

现在把响应代码都提取到了线程类中了，start方法里就不用出现响应代码了，只要调用线程类执行响应过程就可以了，这时用线程池的技术，执行线程类。

### 声明线程池对象

### 在构造函数中初始化线程池对象

### 在start方法中执行线程类

```java
package cn.tedu.core;

import java.io.IOException;

import [java.io.OutputStream];

import [java.io.PrintStream];

import java.net.ServerSocket;

import java.net.Socket;

import java.util.concurrent.ExecutorService;

import java.util.concurrent.Executors;

/**

这个类用来代表服务器端的程序

4.1.1 声明ServerSocket对象

4.1.2 在构造函数中完成ServerSocket对象的初始化

4.1.3 提供start方法，接收客户端的请求并响应

4.1.4 提供main方法，启动服务器

*/

public class WebServer {

//1,声明ServerSocket对象

private ServerSocket server;

//i,,,,声明线程池对象

private ExecutorService threadPool;

//2,在构造函数中完成ServerSocket对象的初始化

public WebServer(){

try {

//绑定端口号

server = new ServerSocket(8080);

//i,,,,初始化线程池对象，最大线程数是100

threadPool =Executors.newFixedThreadPool(100);

System.out.println("start server...");

} catch (IOException e) {

e.printStackTrace();

}

}

//3,提供start方法，接收客户端的请求并响应

public void start(){

try {

while(true){

//持续接收客户端请求

Socket socket = server.accept();

//i,,,,用线程池来执行线程类

threadPool.execute(new ClientHandler(socket));

//向浏览器输出一句话hello

/*组织的数据格式不符合[http]协议

OutputStream out = socket.getOutputStream();

out.write("hello".getBytes());

out.flush();*/

/*//改造输出数据的格式

PrintStream [ps] =

new PrintStream(

socket.getOutputStream());

//拼接状态行

ps.println("HTTP/1.1 200 OK");

//拼接响应头，响应内容：网页类型

ps.println("Content-Type:text/[html]");

//拼接响应头，响应内容的长度

String data = "hello server~";

ps.println("Content-Length:"+data.length());

//拼接空白行

ps.println();

//拼接响应实体内容（真正要显示的内容）

ps.write(data.getBytes());

ps.flush();

socket.close();*/

}

} catch (IOException e) {

e.printStackTrace();

}

}

//4,提供main方法，启动服务器

public static void main(String[] args) {

WebServer server = new WebServer();

//接收客户端请求，并响应

server.start();

}

}
```

### 测试

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image008.png?raw=true)

响应网页文件
------------

到目前为止，实现了服务器的基本功能，可以用来接收客户端的请求，并作出响应，但是只是响应一个字符串。最终我们想要实现向客户端响应一个网页。

### 创建网页文件index.html

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image009.png?raw=true)

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image010.png?raw=true)

### 改造run方法，可以响应网页文件

3,重写run方法，抽取start方法

```java
@Override

public void run() {

try {

//抽取响应代码

PrintStream ps =new PrintStream(socket.getOutputStream());

//拼接状态行

ps.println("HTTP/1.1 200 OK");

//拼接响应头，响应类型：网页

ps.println("Content-Type:text/html");

//拼接响应头，响应数据的长度

// String data = "hello server~";

// ps.println("Content-Length:"+data.length());

//a,,,,,,,,响应网页文件

File file = new File("WebContent/index.html");

ps.println("Content-Length:"+file.length());

//空白行

ps.println();

//真要显示的数据,显示网页文件

//读取文件，读到数组里

BufferedInputStream bis =new BufferedInputStream(new FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

//写出去

ps.write(b);

// ps.write(data.getBytes());

ps.flush();

socket.close();

} catch (IOException e) {

e.printStackTrace();

}

}
```

### 测试

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image011.png?raw=true)

动态响应网页
------------

### 创建test.html

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image012.png?raw=true)

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image013.png?raw=true)

### 改造run方法，动态的拼接文件路径

```java
package cn.tedu.core;

import java.io.BufferedInputStream;

import java.io.BufferedReader;

import java.io.File;

import java.io.FileInputStream;

import java.io.IOException;

import java.io.InputStreamReader;

import java.io.PrintStream;

import java.net.Socket;

/**

这个类用来作为线程类，抽取start方法的代码 4.2.1 声明Socket对象 4.2.2
在构造函数中传入socket对象并保存类中 4.2.3

重写run方法

*/

public class ClientHandler implements Runnable {

// 1,声明Socket对象

private Socket socket;

// 2,在构造函数中传入socket对象并保存类中

public ClientHandler(Socket socket) {

this.socket = socket;

}

// 3,重写run方法，抽取start方法

@Override

public void run() {

try {

// ------获取请求行的数据------

BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));

// 获取请求行信息

// line = GET /index.html HTTP/1.1

String line = reader.readLine();

// 获取请求行的第二块内容

if (line != null) {

// 按照空格切割请求行

// [GET,/index.html,HTTP/1.1]

String[] strs = line.split(" ");

// [uri] = /index.html

String uri = strs[1];

//设置默认主页

if(uri.equals("/")){

uri="/index.html";

}

// -------抽取响应代码------

PrintStream ps = new PrintStream(socket.getOutputStream());

// 拼接状态行

ps.println("HTTP/1.1 200 OK");

// 拼接响应头，响应类型：网页

ps.println("Content-Type:text/html");

// 拼接响应头，响应数据的长度

// String data = "hello server~";

// ps.println("Content-Length:"+data.length());

// a,,,,,,,,响应网页文件

// File file = new File("WebContent/index.html");

File file = new File("WebContent" + uri);

ps.println("Content-Length:" + file.length());

// 空白行

ps.println();

// 真要显示的数据,显示网页文件

// 读取文件，读到数组里

BufferedInputStream [bis] = new
BufferedInputStream(new FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

// 写出去

ps.write(b);

// ps.write(data.getBytes());

ps.flush();

socket.close();

}

} catch (IOException e) {

e.printStackTrace();

}

}

}
```

### 测试

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image014.png?raw=true)

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image015.png?raw=true)

BUG
===

浏览器自动拼接前缀后缀
----------------------

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image016.png?raw=true)

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image017.png?raw=true)

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image018.png?raw=true)

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image019.png?raw=true)

FileNotFoundException
---------------------

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image020.png?raw=true)

Ico文件找不到
-------------

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image021.png?raw=true)

中文乱码
--------

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver01.files/image022.png?raw=true)




代码实现V2.0 -- 代码重构
=========================

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver02.files/image002.png?raw=true)

创建HttpRequest类
-----------------

封装请求信息的，优化run方法。

### 声明请求参数

### 在构造函数中初始化参数

```java
package cn.tedu.http;

import java.io.BufferedReader;

import java.io.IOException;

import java.io.InputStream;

import java.io.InputStreamReader;

/**

这个类用来封装请求信息

1.1.1 声明请求参数

1.1.2 在构造函数中初始化参数

1.1.3 改造run方法

*/

public class HttpRequest {

//1,声明请求参数，设置getter setter方法

// 请求方式

private String method;

// 请求资源的路径

private String uri;

// 请求遵循的协议名版本号

private String protocol;

//2,在构造函数中初始化参数

public HttpRequest(InputStream in){

try {

//获取输入流的信息

BufferedReader reader =new BufferedReader(new InputStreamReader(in));

//读取请求行

//GET /test.html HTTP/1.1

String line = reader.readLine();

// 防止尝试连接的空指针异常

if (line != null && line.length() >0) {

// 用空格切割字符串返回数组

// [GET,/test.html,HTTP/1.1]

String[] datas = line.split(" ");

// 给成员变量赋值

method = datas[0];

uri = datas[1];

// 设置默认主页

if (uri.equals("/")) {

uri = "/index.html";

}

protocol = datas[2];

} catch (IOException e) {

e.printStackTrace();

}

}

//getters setters

public String getMethod() {

return method;

}

public void setMethod(String method) {

this.method = method;

}

public String getUri() {

return uri;

}

public void setUri(String uri) {

this.uri = uri;

}

public String getProtocol() {

return protocol;

}

public void setProtocol(String protocol) {

this.protocol = protocol;

}

}
```

### 改造run方法

```java
package cn.tedu.core;

import java.io.BufferedInputStream;

import java.io.BufferedReader;

import java.io.File;

import java.io.FileInputStream;

import java.io.IOException;

import java.io.InputStreamReader;

import java.io.PrintStream;

import java.net.Socket;

import cn.tedu.http.HttpRequest;

/**

\* 这个类用来作为线程类，抽取start方法的代码 4.2.1 声明Socket对象 4.2.2
在构造函数中传入socket对象并保存类中 4.2.3

\* 重写run方法

*/

public class ClientHandler implements Runnable {

// 1,声明Socket对象

private Socket socket;

// 2,在构造函数中传入socket对象并保存类中

public ClientHandler(Socket socket) {

this.socket = socket;

}

// 3,重写run方法，抽取start方法

@Override

public void run() {

try {

// ------获取请求行的数据------

HttpRequest request = new HttpRequest(socket.getInputStream());

// 浏览器和服务器尝试连接时会产生uri为空的现象

if (request.getUri() != null && request.getUri().length() >0) {

// -------抽取响应代码------

PrintStream ps = new PrintStream(socket.getOutputStream());

// 拼接状态行

ps.println("HTTP/1.1 200 OK");

// 拼接响应头，响应类型：网页

ps.println("Content-Type:text/html");

// 拼接响应头，响应数据的长度

// String data = "hello server~";

// ps.println("Content-Length:"+data.length());

// a,,,,,,,,响应网页文件

// File file = new File("WebContent/index.html");

File file = new File("WebContent" + request.getUri());

ps.println("Content-Length:" + file.length());

// 空白行

ps.println();

// 真要显示的数据,显示网页文件

// 读取文件，读到数组里

BufferedInputStream bis = new BufferedInputStream(new
FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

// 写出去

ps.write(b);

// ps.write(data.getBytes());

ps.flush();

socket.close();

}

} catch (

IOException e) {

e.printStackTrace();

}

}

}
```

创建HttpResponse类
------------------

封装响应信息，优化run方法

### 封装4个响应参数 setter getter

### 封装OutputStream属性。Setter getter

### 改造getOutputStream方法

```java
package cn.tedu.http;

import java.io.OutputStream;

/**

\* 这个类用来封装响应信息

1.2.1 封装4个响应参数 setter getter

1.2.2 封装OutputStream属性。Setter getter

1.2.3 改造getOutputStream方法

1.2.4 改造run方法

*/

public class HttpResponse {

//1,封装4个响应参数 setter getter

//响应遵循的协议名版本号

private String protocol;

//状态码

private int status;

//响应的类型

private String contentType;

//响应数据的长度

private int contentLength;

//2,封装OutputStream属性 Setter getter

private OutputStream out;

public HttpResponse(

OutputStream out){

this.out=out;

}

//3,改造getOut方法

//保证响应头只被发送一次

private boolean isSend;//默认false

public OutputStream getOut() {

if(!isSend){//如果没有被发送过就发一次

//抽取响应头代码...

PrintStream ps = new PrintStream(out);

//状态行

ps.println(protocol+" "+status+" OK");

//响应头

ps.println("Content-Type:"+contentType);

ps.println("Content-Length:"+contentLength);

//空白行

ps.println();

isSend=true;//改变状态

}

return out;

}

public void setOut(OutputStream out) {

this.out = out;

}

//setter getter

public String getProtocol() {

return protocol;

}

public void setProtocol(String protocol) {

this.protocol = protocol;

}

public int getStatus() {

return status;

}

public void setStatus(int status) {

this.status = status;

}

public String getContentType() {

return contentType;

}

public void setContentType(String contentType) {

this.contentType = contentType;

}

public int getContentLength() {

return contentLength;

}

public void setContentLength(int contentLength) {

this.contentLength = contentLength;

}

}

```
### 改造run方法

```java
package cn.tedu.core;

import java.io.BufferedInputStream;

import java.io.File;

import java.io.FileInputStream;

import java.io.IOException;

import java.net.Socket;

import cn.tedu.http.HttpRequest;

import cn.tedu.http.HttpResponse;

/**

\* 这个类用来作为线程类，抽取start方法的代码 4.2.1 声明Socket对象 4.2.2
在构造函数中传入socket对象并保存类中 4.2.3

\* 重写run方法

*/

public class ClientHandler implements Runnable {

// 1,声明Socket对象

private Socket socket;

// 2,在构造函数中传入socket对象并保存类中

public ClientHandler(Socket socket) {

this.socket = socket;

}

// 3,重写run方法，抽取start方法

@Override

public void run() {

try {

// ------获取请求行的数据------

HttpRequest request = new HttpRequest(socket.getInputStream());

// 浏览器和服务器尝试连接时会产生[uri]{.underline}为空的现象

if (request.getUri() != null && request.getUri().length() >0)
{

File file = new File("WebContent" + request.getUri());

// -------抽取响应代码------

HttpResponse response =

new HttpResponse(

socket.getOutputStream());

//给变量赋值

response.setProtocol("HTTP/1.1");//协议

response.setStatus(200);//状态码

response.setContentType("text/html");

response.setContentLength((int) file.length());

// 真要显示的数据,显示网页文件

// 读取文件，读到数组里

BufferedInputStream [bis]{.underline} = new
BufferedInputStream(new FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

// 写出去

response.getOut().write(b);

// ps.write(data.getBytes());

response.getOut().flush();

socket.close();

}

} catch (

IOException e) {

e.printStackTrace();

}

}

}
```

代码实现V3.0 -- 代码优化
=========================

提取服务器参数
--------------

提取4个参数到配置文件中，config/server.xml中.

Xml是一个标记语言，由大量的标记组成的。开始标记和结束标记匹配。

Xml是可拓展性的标记语言，支持自定义内容。

每个标记都可以存在属性，属性的名字和属性的值要用=连接。属性的值用双引号或者单引号给引起来。

### 创建server.xml文件

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver02.files/image003.png?raw=true)

### 准备数据

```xml
<?xml version="1.0" encoding="UTF-8"?>

<server>

<service>

<connect port="8080"

pool="100"

webroot="WebContent"

protocol="HTTP/1.1"></connect>

</service>

</server>
```

### 创建ServerContext类，读取xml的数据

```java
package cn.tedu.common;

import org.dom4j.io.SAXReader;

/**

\* 这个类用来读取server.xml的数据

1,声明4个参数

2,在静态代码块中初始化参数

*/

public class ServerContext {

//1,声明4个参数

//服务器的端口号

public static int port;

//线程数

public static int pool;

//服务器的资源根目录

public static String webRoot;

//协议名版本号

public static String protocol;

//2,在静态代码块中初始化参数

static{

init();

}

//读取server.xml中的数据，使用dom4j技术

private static void init() {

try {

SAXReader reader = new SAXReader();

//读取xml文件

Document doc = reader.read("config/server.xml");

//获取根节点server

Element server = doc.getRootElement();

//读取子节点service

Element service = server.element("service");

//读取子节点connect

Element connect = service.element("connect");

//获取connect元素的属性

port=Integer.valueOf(connect.attributeValue("port"));

pool=Integer.valueOf(connect.attributeValue("pool"));

webRoot=connect.attributeValue("webroot");

protocol=connect.attributeValue("protocol");

} catch (DocumentException e) {

e.printStackTrace();

} 

}

}

```
### 改造run方法

```java
package cn.tedu.core;

import java.io.BufferedInputStream;

import java.io.File;

import java.io.FileInputStream;

import java.io.IOException;

import java.io.OutputStream;

import java.net.Socket;

import cn.tedu.common.ServerContext;

import cn.tedu.http.HttpRequest;

import cn.tedu.http.HttpResponse;

/**

\* 这个类用来作为线程类，抽取start方法的代码 4.2.1 声明Socket对象 4.2.2
在构造函数中传入socket对象并保存类中 4.2.3

\* 重写run方法

*/

public class ClientHandler implements Runnable {

// 1,声明Socket对象

private Socket socket;

// 2,在构造函数中传入socket对象并保存类中

public ClientHandler(Socket socket) {

this.socket = socket;

}

// 3,重写run方法，抽取start方法

@Override

public void run() {

try {

// ------获取请求行的数据------

HttpRequest request = new HttpRequest(socket.getInputStream());

// 浏览器和服务器尝试连接时会产生uri为空的现象

if (request.getUri() != null && request.getUri().length() >0) {

File file = new File(ServerContext.webRoot + request.getUri());

// -------抽取响应代码------

HttpResponse response =new HttpResponse(socket.getOutputStream());

//给变量赋值

response.setProtocol(ServerContext.protocol);//协议

response.setStatus(200);//状态码

response.setContentType("text/html");

response.setContentLength((int) file.length());

// 真要显示的数据,显示网页文件

// 读取文件，读到数组里

BufferedInputStream bis = new BufferedInputStream(new
FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

// 写出去

OutputStream out1 = response.getOut();

out1.write(b);

// ps.write(data.getBytes());

response.getOut().flush();

socket.close();

}

} catch (

IOException e) {

e.printStackTrace();

}

}

}
```

### 改造WebServer类

```java
package cn.tedu.core;

import java.io.IOException;

import java.io.OutputStream;

import java.io.PrintStream;

import java.net.ServerSocket;

import java.net.Socket;

import java.util.concurrent.ExecutorService;

import java.util.concurrent.Executors;

import cn.tedu.common.ServerContext;

/**

\* 这个类用来代表服务器端的程序

4.1.1 声明ServerSocket对象

4.1.2 在构造函数中完成ServerSocket对象的初始化

4.1.3 提供start方法，接收客户端的请求并响应

4.1.4 提供main方法，启动服务器

*/

public class WebServer {

//1,声明ServerSocket对象

private ServerSocket server;

//i,,,,声明线程池对象

private ExecutorService threadPool;

//2,在构造函数中完成ServerSocket对象的初始化

public WebServer(){

try {

//绑定端口号

server = new ServerSocket(ServerContext.port);

//i,,,,初始化线程池对象，最大线程数是100

threadPool = Executors.newFixedThreadPool(ServerContext.pool);

System.out.println("start server...");

} catch (IOException e) {

e.printStackTrace();

}

}

//3,提供start方法，接收客户端的请求并响应

public void start(){

try {

while(true){

//持续接收客户端请求

Socket socket = server.accept();

//i,,,,用线程池来执行线程类

threadPool.execute(new ClientHandler(socket));

//向浏览器输出一句话hello

/*组织的数据格式不符合http协议

OutputStream out = socket.getOutputStream();

out.write("hello".getBytes());

out.flush();*/

/*改造输出数据的格式

PrintStream ps =new PrintStream(socket.getOutputStream());

//拼接状态行

ps.println("HTTP/1.1 200 OK");

//拼接响应头，响应内容：网页类型

ps.println("Content-Type:text/html");

//拼接响应头，响应内容的长度

String data = "hello server~";

ps.println("Content-Length:"+data.length());

//拼接空白行

ps.println();

//拼接响应实体内容（真正要显示的内容）

ps.write(data.getBytes());

ps.flush();

socket.close();*/

}

} catch (IOException e) {

e.printStackTrace();

}

}

//4,提供main方法，启动服务器

public static void main(String[] args) {

WebServer server = new WebServer();

//接收客户端请求，并响应

server.start();

}

}

```
配置404页面
-----------

当网站出现了异常时，应该做统一的错误处理页面，不要把异常直接暴露给客户端。

### 创建404.html页面

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver02.files/image004.png?raw=true)

### 改造server.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<server>

<service>

<connect port="8080"

pool="100"

webroot="WebContent"

protocol="HTTP/1.1"></connect>

<!-- 配置404页面 -->

<page404>404.html</page404>

</service>

</server>
```

### 改造ServerContext类

```java
package cn.tedu.common;

import org.dom4j.Document;

import org.dom4j.DocumentException;

import org.dom4j.Element;

import org.dom4j.io.SAXReader;

/**

\* 这个类用来读取server.xml的数据

1,声明4个参数

2,在静态代码块中初始化参数

*/

public class ServerContext {

//1,声明4个参数

//服务器的端口号

public static int port;

//线程数

public static int pool;

//服务器的资源根目录

public static String webRoot;

//协议名版本号

public static String protocol;

//a，，，，，声明404页面

public static String notFoundPage;

//2,在静态代码块中初始化参数

static{

init();

}

//读取server.xml中的数据，使用dom4j技术

private static void init() {

try {

SAXReader reader = new SAXReader();

//读取[xml]文件

Document doc = reader.read("config/server.xml");

//获取根节点server

Element server = doc.getRootElement();

//读取子节点service

Element service = server.element("service");

//读取子节点connect

Element connect = service.element("connect");

//获取connect元素的属性给成员变量赋值

//attributeValue传入的值是：[xml]中属性的名字

port=Integer.valueOf(connect.attributeValue("port"));

pool=Integer.valueOf(connect.attributeValue("pool"));

webRoot=connect.attributeValue("webroot");

protocol=connect.attributeValue("protocol");

//a,,,,获取page404的值，notFoundPage赋值

notFoundPage = service.elementText("page404");

} catch (DocumentException e) {

e.printStackTrace();

}

}

}
```

### 改造run方法

// 3,重写run方法，抽取start方法

```java
@Override

public void run() {

try {

// ------获取请求行的数据------

HttpRequest request = new HttpRequest(socket.getInputStream());

// 浏览器和服务器尝试连接时会产生[uri]{.underline}为空的现象

if (request.getUri() != null && request.getUri().length() >0)
{

// -------抽取响应代码------

HttpResponse response =new HttpResponse(socket.getOutputStream());

File file = new File(ServerContext.webRoot + request.getUri());

//a，，，配置404页面

if(!file.exists()){

//如果文件不存在跳转至404页面

file = new File(ServerContext.webRoot+"/"+ServerContext.notFoundPage);

//修改状态码

response.setStatus(404);

}else{

response.setStatus(200);//状态码

}

//给变量赋值

response.setProtocol(ServerContext.protocol);//协议

response.setContentType("text/html");

response.setContentLength((int) file.length());

// 真要显示的数据,显示网页文件

// 读取文件，读到数组里

BufferedInputStream [bis]{.underline} = new
BufferedInputStream(new FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

// 写出去

OutputStream out1 = response.getOut();

out1.write(b);

// ps.write(data.getBytes());

response.getOut().flush();

socket.close();

}

} catch (

IOException e) {

e.printStackTrace();

}

}
```

响应多类型网页文件
------------------

### 改造server.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>

<server>

<service>

<connect port="8080"

pool="100"

webroot="WebContent"

protocol="HTTP/1.1"></connect>

<!-- 配置404页面 -->

<page404>404.[html]{.underline}</page404>

</service>

<!-- 响应多类型的文件

ext：文件扩展名

type:对应content-type的值

ext的值获取出来作为map的key

type的值获取出来作为map的value

map->{html,text/html,jpg,image/jpeg... }

-->

<type-mappings>

<type-mapping ext="html" type="text/html"></type-mapping>

<type-mapping ext="jpg" type="image/jpeg"></type-mapping>

<type-mapping ext="avi" type="vedio/avi"></type-mapping>

<type-mapping ext="css" type="text/css"></type-mapping>

</type-mappings>

</server>
```

### 改造ServerContext类读取信息

```java
package cn.tedu.common;

import java.util.HashMap;

import java.util.List;

import java.util.Map;

import org.dom4j.Document;

import org.dom4j.DocumentException;

import org.dom4j.Element;

import org.dom4j.io.SAXReader;

/**

\* 这个类用来读取server.xml的数据

1,声明4个参数

2,在静态代码块中初始化参数

*/

public class ServerContext {

//1,声明4个参数

//服务器的端口号

public static int port;

//线程数

public static int pool;

//服务器的资源根目录

public static String webRoot;

//协议名版本号

public static String protocol;

//a，，，，，声明404页面

public static String notFoundPage;

//i,,,,,,,,,封装contenttype的map


/* ext的值获取出来作为map的key

type的值获取出来作为map的value

map->{html,text/html jpg,image/jpeg... }

*/

public static Map<String,String>map = new HashMap<String,String>();

//2,在静态代码块中初始化参数

static{

init();

}

//读取server.xml中的数据，使用dom4j技术

private static void init() {

try {

SAXReader reader = new SAXReader();

//读取xml文件

Document doc = reader.read("config/server.xml");

//获取根节点server

Element server = doc.getRootElement();

//读取子节点service

Element service = server.element("service");

//读取子节点connect

Element connect = service.element("connect");

//获取connect元素的属性给成员变量赋值

//attributeValue传入的值是：xml中属性的名字

port=Integer.valueOf(connect.attributeValue("port"));

pool=Integer.valueOf(connect.attributeValue("pool"));

webRoot=connect.attributeValue("webroot");

protocol=connect.attributeValue("protocol");

//a,,,,获取page404的值，notFoundPage赋值

notFoundPage = service.elementText("page404");

//i,,,,,初始化map的值

List<Element>list = server.element("type-mappings").elements();

//遍历list

for (Element element : list) {

/*

ext的值获取出来作为map的key

type的值获取出来作为map的value

map->{html,text/html jpg,image/jpeg... }

*/

String key =element.attributeValue("ext");

String value =element.attributeValue("type");

//存入map

map.put(key, value);

}

} catch (DocumentException e) {

e.printStackTrace();

}

}

}

```
### 改造run方法

```java
package cn.tedu.core;

import java.io.BufferedInputStream;

import java.io.File;

import java.io.FileInputStream;

import java.io.IOException;

import java.io.OutputStream;

import java.net.Socket;

import cn.tedu.common.ServerContext;

import cn.tedu.http.HttpRequest;

import cn.tedu.http.HttpResponse;

/**

这个类用来作为线程类，抽取start方法的代码 4.2.1 声明Socket对象 4.2.2
在构造函数中传入socket对象并保存类中 4.2.3

重写run方法

*/

public class ClientHandler implements Runnable {

// 1,声明Socket对象

private Socket socket;

// 2,在构造函数中传入socket对象并保存类中

public ClientHandler(Socket socket) {

this.socket = socket;

}

// 3,重写run方法，抽取start方法

@Override

public void run() {

try {

// ------获取请求行的数据------

HttpRequest request = new HttpRequest(socket.getInputStream());

// 浏览器和服务器尝试连接时会产生uri为空的现象

if (request.getUri() != null && request.getUri().length() >0) {

// -------抽取响应代码------

HttpResponse response =new HttpResponse(socket.getOutputStream());

File file = new File(ServerContext.webRoot + request.getUri());

//a，，，配置404页面

if(!file.exists()){

//如果文件不存在跳转至404页面

file = new File(ServerContext.webRoot +"/"+ServerContext.notFoundPage);

//修改状态码

response.setStatus(404);

}else{

response.setStatus(200);//状态码

}

//给变量赋值

response.setProtocol(ServerContext.protocol);//协议

response.setContentType(getContentTypeByFile(file));

response.setContentLength((int) file.length());

// 真要显示的数据,显示网页文件

// 读取文件，读到数组里

BufferedInputStream bis = new BufferedInputStream(new FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

// 写出去

OutputStream out1 = response.getOut();

out1.write(b);

// ps.write(data.getBytes());

response.getOut().flush();

socket.close();

}

} catch (

IOException e) {

e.printStackTrace();

}

}

//根据文件的后缀名，作为map的key去找对应的value

private String getContentTypeByFile(

File file) {

//获取文件名字

//fileName=i.n.de.x.html

String fileName = file.getName();

//获取文件的后缀名(获取到.的位置加1得到html后缀)

String key =fileName.substring(fileName.lastIndexOf(".")+1);

//去map中找value

String val=ServerContext.map.get(key);

return val;

}

}

```



动态响应描述
============

创建HttpContext类
-----------------

声明常量，存入map
-----------------

```java
package cn.tedu.common;

import java.util.HashMap;

import java.util.Map;

/**

 这个类用来封装[http]相关参数

*/

public class HttpContext {

//响应的状态码

public static final int CODE_OK=200;

public static final int CODE_NOTFOUND=404;

public static final int CODE_ERROR=500;

//响应的描述信息

public static final String DESC_OK="OK";

public static final String dESC_NOTFOUND="NOTFOUND";

public static final String DESC_ERROR="ERROR";

//声明descMap

public static Map<Integer,String> descMap= new HashMap<Integer,String>();

//初始化descMap

static{

descMap.put(CODE_OK, DESC_OK);

descMap.put(CODE_NOTFOUND, DESC_NOTFOUND);

descMap.put(CODE_ERROR, DESC_ERROR);

}

}
```

改造HttpResponse类
------------------

```java
package cn.tedu.http;

import java.io.OutputStream;

import java.io.PrintStream;

import cn.tedu.common.HttpContext;

/**

\* 这个类用来封装响应信息

1.2.1 封装4个响应参数 setter getter

1.2.2 封装OutputStream属性。Setter getter

1.2.3 改造getOutputStream方法

1.2.4 改造run方法

*/

public class HttpResponse {

//1,封装4个响应参数 setter getter

//响应遵循的协议名版本号

private String protocol;

//状态码

private int status;

//响应的类型

private String contentType;

//响应数据的长度

private int contentLength;

//2,封装OutputStream属性 Setter getter

private OutputStream out;

public HttpResponse(OutputStream out){

this.out=out;

}

//3,改造getOut方法

//保证响应头只被发送一次

private boolean isSend;//默认false

public OutputStream getOut() {

if(!isSend){//如果没有被发送过就发一次

//抽取响应头代码...

PrintStream ps =new PrintStream(out);

//状态行

ps.println(protocol+" "+status+" "+HttpContext.descMap.get(status));

//响应头

ps.println("Content-Type:"+contentType);

ps.println("Content-Length:"+contentLength);

//空白行

ps.println();

isSend=true;//改变状态

}

return out;

}

public void setOut(OutputStream out) {

this.out = out;

}

//setter getter

public String getProtocol() {

return protocol;

}

public void setProtocol(String protocol) {

this.protocol = protocol;

}

public int getStatus() {

return status;

}

public void setStatus(int status) {

this.status = status;

}

public String getContentType() {

return contentType;

}

public void setContentType(String contentType) {

this.contentType = contentType;

}

public int getContentLength() {

return contentLength;

}

public void setContentLength(int contentLength) {

this.contentLength = contentLength;

}

}
```

改造ClienHandler类
------------------

```java
package cn.tedu.core;

import java.io.BufferedInputStream;

import java.io.File;

import java.io.FileInputStream;

import java.io.IOException;

import java.io.OutputStream;

import java.net.Socket;

import cn.tedu.common.HttpContext;

import cn.tedu.common.ServerContext;

import cn.tedu.http.HttpRequest;

import cn.tedu.http.HttpResponse;

/**

\* 这个类用来作为线程类，抽取start方法的代码 4.2.1 声明Socket对象 4.2.2
在构造函数中传入socket对象并保存类中 4.2.3

\* 重写run方法

*/

public class ClientHandler implements Runnable {

// 1,声明Socket对象

private Socket socket;

// 2,在构造函数中传入socket对象并保存类中

public ClientHandler(Socket socket) {

this.socket = socket;

}

// 3,重写run方法，抽取start方法

@Override

public void run() {

try {

// ------获取请求行的数据------

HttpRequest request = new HttpRequest(socket.getInputStream());

// 浏览器和服务器尝试连接时会产生uri为空的现象

if (request.getUri() != null && request.getUri().length() > 0) {

// -------抽取响应代码------

HttpResponse response =new HttpResponse(socket.getOutputStream());

File file = new File(ServerContext.webRoot + request.getUri());

//a，，，配置404页面

if(!file.exists()){

//如果文件不存在跳转至404页面

file = new File(ServerContext.webRoot+"/"+ServerContext.notFoundPage);

//修改状态码

response.setStatus(HttpContext.CODE_NOTFOUND);

}else{

response.setStatus(HttpContext.CODE_OK);//状态码

}

//给变量赋值

response.setProtocol(ServerContext.protocol);//协议

response.setContentType(getContentTypeByFile(file));

response.setContentLength((int) file.length());

// 真要显示的数据,显示网页文件

// 读取文件，读到数组里

BufferedInputStream bis = new BufferedInputStream(new FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

// 写出去

OutputStream out1 = response.getOut();

out1.write(b);

// ps.write(data.getBytes());

response.getOut().flush();

socket.close();

}

} catch (

IOException e) {

e.printStackTrace();

}

}

//根据文件的后缀名，作为map的key去找对应的value

private String getContentTypeByFile(

File file) {

//获取文件名字

//fileName=i.n.de.x.html

String fileName = file.getName();

//获取文件的后缀名(获取到.的位置加1得到html后缀)

String key =

fileName.substring(

fileName.lastIndexOf(".")+1);

//去map中找value

String val=ServerContext.map.get(key);

return val;

}

}
```

代码实现V4.0 -- 实现注册或登录
===============================

准备页面
--------

### 提供注册（regist.html）和登录页面(login.html)

1、在WebContent目录下创建regist.html页面, 内容实现如下:

```html
<!DOCTYPE html>

<html>

<head>

<meta charset="UTF-8">

<title>欢迎注册</title>

</head>

<body>

<h1>欢迎注册</h1>

<form action="http://localhost:8080/RegistUser">

用户名：<input type="text" name="username"/>

<br/><br/>

密&nbsp;码：<input type="password" name="password"/>

<input type="submit" value="注册"/>

</form>

</body>

</html>

```
 

2、在WebContent目录下创建login.html页面, 内容实现如下:

```html
<!DOCTYPE html>

<html>

<head>

<meta charset="UTF-8">

<title>Insert title here</title>

</head>

<body>

<h1>欢迎登录</h1>

<form action="http://localhost:8080/LoginUser">

用户名：<input type="text" name="username"/>

<br/><br/>

密&nbsp;码：<input type="password" name="password"/>

<input type="submit" value="登录"/>

</form>

</body>

</html>
```

### 提供注册成功和登录成功后的提示页面

1、在WebContent目录下创建regist_success.html页面, 内容实现如下:

```html
<!DOCTYPE html>

<html>

<head>

<meta charset="UTF-8">

<title>regist_success</title>

</head>

<body>

<h1 style=text-align:center;

color:red;margin-top:50px">

恭喜您注册成功！

<br/>

<a href="http://localhost:8080/index.html">

回到主页

</a>

</h1>

</body>

</html>
```

2、在WebContent目录下创建login_success.html页面, 内容实现如下:

```html
<!DOCTYPE html>

<html>

<head>

<meta charset="UTF-8">

<title>login_success</title>

</head>

<body>

<h1 style=text-align:center;

color:red;margin-top:50px">

恭喜您登录成功！


<br/>

<a href="http://localhost:8080/index.html">

回到主页

</a>

</h1>

</body>

</html>
```

![image](https://github.com/leemin01/leemin01.Github.io/blob/master/img/webserver02.files/image008.png?raw=true)

修改HttpRequest类，提供获取参数的方法
-------------------------------------

```java
package cn.tedu.http;

import java.io.BufferedReader;

import java.io.IOException;

import java.io.InputStream;

import java.io.InputStreamReader;

import java.util.HashMap;

import java.util.Map;

/**

\* 这个类用来封装请求信息 1.1.1 声明请求参数 1.1.2
在构造函数中初始化参数 1.1.3 改造run方法

*/

public class HttpRequest {

// 1,声明请求参数，设置getter setter方法

// 请求方式

private String method;

// 请求资源的路径

private String uri;

// 请求遵循的协议名版本号

private String protocol;

//i,,,,,,,声明存放请求参数的map

private Map<String,String> paramMap = new HashMap<String,String>();

// 2,在构造函数中初始化参数

public HttpRequest(InputStream in) {

try {

// 获取输入流的信息

BufferedReader reader = new BufferedReader(new InputStreamReader(in));

// 读取请求行

// GET /test.html HTTP/1.1

String line = reader.readLine();

// 防止尝试连接的空指针异常

if (line != null && line.length() > 0) {

// 用空格切割字符串返回数组

// [GET,/test.html,HTTP/1.1]

String[] datas = line.split(" ");

// 给成员变量赋值

method = datas[0];

uri = datas[1];

protocol = datas[2];

// 设置默认主页

if (uri.equals("/")) {

uri = "/index.html";

}

//i,,,初始化map

//uri=/RegistUser?username=111&password=222

if(uri!=null && uri.contains("?")){

//按照?切割字符串

//[/RegistUser,username=111&password=222]

String[] strs = uri.split("\\\\?");

//big->username=111&password=222

String big = strs[1];

//按照&切割字符串

//[username=111,password=222]

String[] small = big.split("&");

for (String s : small) {

//key->username password

String key = s.split("=")[0];

//value->111 222

String value = s.split("=")[1];

//存入map {username,111 password,222}

paramMap.put(key,value);

}

}

}

} catch (IOException e) {

e.printStackTrace();

}

}

//提供获取请求参数的方法

public String getParameter(String key){

return paramMap.get(key);

}

public String getMethod() {

return method;

}

public void setMethod(String method) {

this.method = method;

}

public String getUri() {

return uri;

}

public void setUri(String uri) {

this.uri = uri;

}

public String getProtocol() {

return protocol;

}

public void setProtocol(String protocol) {

this.protocol = protocol;

}

}
```

改造run方法，完成注册
---------------------

```java
package cn.tedu.core;

import java.io.BufferedInputStream;

import java.io.File;

import java.io.FileInputStream;

import java.io.IOException;

import java.io.OutputStream;

import java.net.Socket;

import java.sql.Connection;

import java.sql.PreparedStatement;

import java.sql.SQLException;

import cn.tedu.common.HttpContext;

import cn.tedu.common.ServerContext;

import cn.tedu.http.HttpRequest;

import cn.tedu.http.HttpResponse;

import cn.tedu.util.JDBCUtils;

/**

\* 这个类用来作为线程类，抽取start方法的代码 4.2.1 声明Socket对象 4.2.2
在构造函数中传入socket对象并保存类中 4.2.3

\* 重写run方法

*/

public class ClientHandler implements Runnable {

// 1,声明Socket对象

private Socket socket;

// 2,在构造函数中传入socket对象并保存类中

public ClientHandler(Socket socket) {

this.socket = socket;

}

// 3,重写run方法，抽取start方法

@Override

public void run() {

try {

// ------获取请求行的数据------

HttpRequest request = new HttpRequest(socket.getInputStream());

// -------抽取响应代码------

HttpResponse response = new HttpResponse(socket.getOutputStream());

// 浏览器和服务器尝试连接时会产生uri为空的现象

if (request.getUri() != null && request.getUri().length() > 0) {

//i,,,,,,实现注册或者登录功能

if(request.getUri().startsWith("/RegistUser")|| request.getUri().startsWith("/LoginUser")){

//完成注册或者登录

service(request,response);

return;

}

File file = new File(ServerContext.webRoot + request.getUri());

//a，，，配置404页面

if(!file.exists()){

//如果文件不存在跳转至404页面

file = new File(ServerContext.webRoot +"/"+ServerContext.notFoundPage);

//修改状态码

response.setStatus(HttpContext.CODE_NOTFOUND);

}else{

response.setStatus(HttpContext.CODE_OK);//状态码

}

//给变量赋值

response.setProtocol(ServerContext.protocol);//协议

response.setContentType(getContentTypeByFile(file));

response.setContentLength((int) file.length());

// 真要显示的数据,显示网页文件

// 读取文件，读到数组里

BufferedInputStream bis = new BufferedInputStream(new FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

// 写出去

OutputStream out1 = response.getOut();

out1.write(b);

// ps.write(data.getBytes());

response.getOut().flush();

socket.close();

}

} catch (

IOException e) {

e.printStackTrace();

}

}

//i,,,,完成注册或者登录

//利用jdbc技术，向user表插入一条记录

//向浏览器响应注册成功页面

private void service(HttpRequest request, HttpResponse response) {

//注册过程...

if(request.getUri().startsWith("/RegistUser")){

Connection conn = null;

PreparedStatement ps = null;

try {

//1,注册驱动 2，获取数据库连接

conn = JDBCUtils.getConnection();

//3，获取传输器

String sql ="insert into user values(null,?,?)";

ps = conn.prepareStatement(sql);

//设置参数

String username =request.getParameter("username");

String password =request.getParameter("password");

ps.setString(1, username);

ps.setString(2, password);

//4,执行SQL

int i = ps.executeUpdate();

System.out.println(i);

//-----响应注册成功页面

response.setProtocol(ServerContext.protocol);

response.setStatus(HttpContext.CODE_OK);

//响应注册成功页面的路径

File file = new File(ServerContext.webRoot+"/regist_success.html");

response.setContentType(getContentTypeByFile(file));

response.setContentLength((int) file.length());

//响应文件

BufferedInputStream bis =

new BufferedInputStream(

new FileInputStream(file));

byte[] b = new byte[(int) file.length()];

bis.read(b);

//写出文件

response.getOut().write(b);

response.getOut().flush();

socket.close();

} catch (SQLException e) {

e.printStackTrace();

}finally{

JDBCUtils.close(null, ps, conn);

}

}else if(request.getUri().startsWith("/LoginUser")){

//登录过程...

}

}

//根据文件的后缀名，作为map的key去找对应的value

private String getContentTypeByFile(

File file) {

//获取文件名字

//fileName=i.n.de.x.html

String fileName = file.getName();

//获取文件的后缀名(获取到.的位置加1得到html后缀)

String key =fileName.substring(fileName.lastIndexOf(".")+1);

//去map中找value

String val=ServerContext.map.get(key);

return val;

}

}


```