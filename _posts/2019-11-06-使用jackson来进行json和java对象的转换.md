---
layout: post
title: "使用jackson来进行json和java对象的转换"
subtitle: ''
author: "baka_sbk"
header-style: text
tags:
  - java
  - 
---
### 添加依赖

```xml
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.15</version>
</dependency>
```

### 实例化
```java
ObjectMapper objectMapper = new ObjectMapper();
```

### 转换
***JAVA对象转JSON***
```java
//JAVA对象转JSON
objectMapping.writeValueAsString(obj)
```

***JSON转JAVA对象***
```java
//JAVA对象转JSON
objectMapping.readValue(str, ojb.class)
```

### 注意事项：
* json字符串中的key应该与java对象的属性名相同
* java对象中属性如果为private，则需要显示生成getter/setter方法；如果属性为public，则可以不必写getter/setter方法
* java对象如果有自定义的构造方法，json字符串转换为java对象时会出错
* 如果json字符串中的属性个数小于java对象中的属性个数，可以顺利转换，java中多的那个属性为null

* 如果json字符串中出现java对象中没有的属性，则在将json转换为java对象时会报错：Unrecognized field, not marked as ignorable
解决方法：

```java
//在目标对象的类级别上添加注解
@JsonIgnoreProperties(ignoreUnknown = true)
```
* java对象名和json中名不一致时解决方法：

```java
//在目标对象的字段级别上添加注解：
@JsonProperty(value = "name")
```