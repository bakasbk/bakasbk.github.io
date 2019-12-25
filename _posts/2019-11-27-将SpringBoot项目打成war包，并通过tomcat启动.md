---
layout: post
title: "将SpringBoot项目打成war包，并通过tomcat启动"
subtitle: ''
author: "baka_sbk"
header-style: text
tags:
  - Java
  - Tomcat
  - Operation and maintenance
  - SpringBoot
  - Notes
---

## 修改pom.xml

**修改打包形式**

`<packaging>war</packaging>`

**移除SpringBoot自带的tomcat**

​	找到 *spring-boot-starter-web* 节点，添加 **\<exclusions>** ： 

```xml
            <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <!-- 移除嵌入式tomcat插件 -->
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

**添加servlet-api依赖**

```xml
<dependency>
 <groupId>org.apache.tomcat</groupId>
 <artifactId>tomcat-servlet-api</artifactId>
 <version>8.0.36</version>
 <scope>provided</scope>
</dependency>
```

## 更改启动类

​	在启动类*MyWebApplication.class* 同级包下，新建一个*Tomcat.class* (类名无所谓)

```java
/**
 * @author Bokai Sun
 * @version 1.0
 * @date 2019/11/27 11:00
 */
//继承SpringBootServletInitializer，并重写configure方法
public class Tomcat  extends SpringBootServletInitializer {
    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
        //这里需要指向包含main方法的启动类
        return builder.sources(MyWebApplication.class);
    }
}

```

## 跳过test

在IDEA中，选择*File* -> *Settings* -> *Build,Excecution,Deployment* -> *Build Tools* -> *Maven* -> *Runner* 

勾选 *Skip tests*

## 项目打包

 点击 *Maven* -> *my-web*(项目名) -> *Lifecycle* -> *package* 

将项目打包，如果没有跳过test，此处可能会报错。

显示 `[INFO] BUILD SUCCESS`后，在项目路径下的target文件夹中，把war包copy到tomcat的webapps文件夹下，启动tomcat，即可通过ip地址:8080/项目名访问。

## 可能的报错

 **`（Cannot determine embedded database driver class for database type NONE）`**

网上查到通过tomcat启动SpringBoot项目时，可能会报错 `（Cannot determine embedded database driver class for database type NONE）` 

出现这种情况，在启动类加入注解` @EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class}) `即可。

因为本人没有遇到此问题，没有经过检验。

**其他database报错**

如果遇到其他有关database的报错，请检查*application.properties*中是否配置jdbc

## 完整的pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.1.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.sbk</groupId>
    <artifactId>my-web</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>my-web</name>
    <description>Demo project for Spring Boot</description>
    <packaging>war</packaging>

    <properties>
        <java.version>1.8</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <!-- 移除嵌入式tomcat插件 -->
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.mybatis.spring.boot</groupId>
            <artifactId>mybatis-spring-boot-starter</artifactId>
            <version>2.1.1</version>
        </dependency>

        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <optional>true</optional>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <!-- shiro 相关包 -->
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-core</artifactId>
            <version>1.4.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-spring</artifactId>
            <version>1.4.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.shiro</groupId>
            <artifactId>shiro-ehcache</artifactId>
            <version>1.4.0</version>
        </dependency>
        <!--END-->
        <!--thymeleaf-shiro依赖-->
        <dependency>
            <groupId>com.github.theborakompanioni</groupId>
            <artifactId>thymeleaf-extras-shiro</artifactId>
            <version>2.0.0</version>
        </dependency>
        <!--ehcache依赖-->
        <dependency>
            <groupId>net.sf.ehcache</groupId>
            <artifactId>ehcache</artifactId>
            <version>2.10.2</version>
        </dependency>
        <!--END-->

        <!--tomcat依赖-->
        <dependency>
            <groupId>org.apache.tomcat</groupId>
            <artifactId>tomcat-servlet-api</artifactId>
            <version>8.0.36</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>

</project>

```

tomcat运行成功
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191127115158636.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTQ2MjQ0NDc=,size_16,color_FFFFFF,t_70)
正常访问
![在这里插入图片描述](https://img-blog.csdnimg.cn/20191127115616137.png)