---
layout: post
title: "腾讯云CentOs7安装Mysql+JDK8的全过程"
subtitle: ''
author: "baka_sbk"
header-style: text
tags:
  - CentOS
  - Operation and maintenance
  - Notes
---
### 登陆前需重置密码
先在**腾讯云**→**控制台**→**实例** →**密码/密钥**→选择**重置密码**，然后设置一个root密码，因为是云服务器，所以务必设置**高强度密码**。

### 安装Mysql
yum安装Mysql
```bash
yum install mysql mysql-devel mariadb-server mariadb –y
```
启动服务

```bash
systemctl start mariadb
```
更改数据库root密码

```bash
mysqladmin -u root password [password]
```
更改字符集编码

```bash
vim /etc/my.cnf
```
在配置文件底部增加

```bash
default-character-set =utf8
```

### 安装JDK8
通过FTP将jdk-8u171-linux-x64.tar.gz上传至/usr/local/src，并解压

```bash
 tar xzf jdk-8u171-linux-x64.tar.gz
```
重命名解压出来的文件夹

```bash
 mv jdk1.8.0_171 jdk8
```
配置环境变量

```bash
vim /etc/profile
```
在文件末尾增加以下内容

```bash
JAVA_HOME=/usr/local/src/jdk8
JRE_HOME=/usr/local/src/jdk8/jre
CLASS_PATH=.:$JAVA_HOME/lib
PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH
export JAVA_HOME JRE_HOME PATH CLASS_PATH 
```
保存退出后，刷新配置

```bash
source /etc/profile
```
测试是否安装成功
```bash
[root@VM_0_14_centos src]# java -version
java version "1.8.0_171"
Java(TM) SE Runtime Environment (build 1.8.0_171-b11)
Java HotSpot(TM) 64-Bit Server VM (build 25.171-b11, mixed mode)

```

