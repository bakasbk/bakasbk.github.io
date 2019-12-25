---
layout: post
title: "SpringBoot打成war包后 访问controller 404错误"
subtitle: ''
author: "baka_sbk"
header-style: text
tags:
  - Java
  - Notes
  - Operation and maintenance
  - SpringBoot  
---

#### 背景

根据 [将SpringBoot项目打成war包，并通过tomcat启动](https://blog.csdn.net/u014624447/article/details/103273341) 的方法，将SpringBoot项目打成war包，并在tomcat中成功启动，主页也访问成功了。但是访问controller时会发生404错误。

#### 原因

首先看一下代码：

```html
<!--html页面代码 -->
<form id="reg_form" method="post" action="/reg">
    <!--省略代码-->
</form>
```

此时访问地址为 [http://localhost:8080/reg](http://localhost:8080/reg)

form表单提交后访问"/reg"，这个路径在打成jar包的情况下是可以正常访问的。

```java
//controller代码
@RequestMapping(value = "/reg", method = RequestMethod.POST)
public String register(){
    //省略代码
}
```

但打成war包放入**tomcat/webapps/**  后，此时正确访问controller的路径应为： [http://localhost:8080/项目名/reg]( http://localhost:8080/项目名/reg) 

但实际上的访问路径却是[http://localhost:8080/reg](http://localhost:8080/reg) ，因为路径错误，理所当然的访问不到controller。

#### 解决办法

只需要在原先请求的url之前增加“/项目名/”即可。修改后的代码如下：

```html
<!--修改后的html页面代码 -->
<form id="reg_form" method="post" action="/项目名/reg">
    <!--省略代码-->
</form>
```



<form id="reg_form" method="post" action="/reg">
    <!--省略代码-->
</form>

#### 注意事项
1.	在application.properties中

	```bash
		#设置项目的实际路径
		server.servlet.context-path=/项目名
	```
2. 在pom.xml中设置项目名，与上面相同

	```xml
	<finalName>fw2xk</finalName>
	```
3.	打包前先clean
