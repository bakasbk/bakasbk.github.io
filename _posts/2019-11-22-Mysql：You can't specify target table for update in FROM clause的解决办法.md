---
layout: post
title: "Mysql：You can't specify target table for update in FROM clause的解决办法"
subtitle: ''
author: "baka_sbk"
header-style: text
tags:
  - Mysql
  - Notes
---

> You can't specify target table ‘表名’ for update in FROM clause

意思是  **不能先select出同一表中的某些值，再update这个表**

 **原SQL语句：**
```sql
update 
	push_to_cloud 
set 
	is_confirm = 1 , direction = 1 
where 
	student_code = 01 
and 
	`snap_time`= (
		select max(`snap_time`)from push_to_cloud 
	)
```

出现这种情况，只需要通过中间表select一遍就可以了。

**解决办法：**

```sql
update 
	push_to_cloud 
set 
	is_confirm=1,direction=1 
WHERE 
	student_code = 01 
and 
	`snap_time` =(
		select a.time from (
			select MAX(snap_time) time from push_to_cloud 
		) a 
	)
```
如果出现

> Every derived table must have its own alias

检查是不是没有给表取别名