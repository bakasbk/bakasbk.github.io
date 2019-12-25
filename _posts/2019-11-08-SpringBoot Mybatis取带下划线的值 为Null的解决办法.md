---
layout: post
title: "SpringBoot Mybatis取带下划线的值 为Null的解决办法"
subtitle: ''
author: "baka_sbk"
header-style: text
tags:
  - Java
  - Mybatis
  - SpringBoot
  - Notes笔记
---

### 背景情况

使用Mybatis逆向生成了POJO类，但因为需要接受前端传来的JSON对象，JSON对象中包含下划线，为了图省事，在 ***generatorConfig.xml*** 中设置了

```xml	
<!--使用实际的字段名-->
<property name="useActualColumnNames" value="true"/>
```
让POJO类，JSON对象，MYSQL数据库中都带有下划线，方便进行自动转换。
但没想到在mapper类中编写查询语句后，取到的所有带下划线的值全部为**NULL**
```java
 @Select("select * from box_pushed_data WHERE `trigger`= (select max(`trigger`) FROM box_pushed_data where person_name = #{studentCode})")
    BoxPushedData selectLatestArriveBoxPushedDataByStudentCode(@Param("studentCode") String studentCode);
```
查阅资料后发现是因为：

> 由于 进行查询的时候，返回值是使用的resultType 对应的是实体，而这样create_date与createDate就不能一一对应，框架没那么智能。改为resultMap进行映射即可。
参考资料：[Mybatis 下划线字段为NULL](https://blog.csdn.net/u014042066/article/details/75292724)

故不用注解写查询语句，将查询语句写在**mapper.xml**中即可正常取值

```xml
 <select id="selectLatestArriveBoxPushedDataByStudentCode" resultMap="BaseResultMap">
    select 
   	 	* 
    from
    	box_pushed_data
    where 
    	`trigger` =
    	(select
    	 	max(`trigger`) 
	     from 
	    	 box_pushed_data 
	     where 
	     	person_name = #{studentCode}
     	)

  </select>
```

### 总结
**使用mybatis时少用注解写SQL，在Mapper.xml中编写SQL更加灵活并且利于维护。**