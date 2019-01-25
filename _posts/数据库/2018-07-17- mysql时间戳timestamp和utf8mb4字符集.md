---
layout: post
title:  "mysql 时间戳timestamp和utf8mb4字符集"
date:   2018-07-17
desc: "mysql 时间戳timestamp和utf8mb4字符集"
keywords: "mysql,timestamp,utf8mb4"
categories: [Sql]
tags: [sql, mysql, timestamp]
---

# mysql时间戳timestamp和utf8mb4字符集

##1.timestamp时间戳

timestamp时间戳，可以用来作为单据的创建和更新时间，建表的时候数据格式设置好时间戳格式，如下： 

	`createtime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,    
	在创建新记录的时候把这个字段设置为当前时间，但以后修改时，不再刷新它        
	`modifytime` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,    
	在创建新记录和修改现有记录的时候都对这个数据列刷新

之后这两个字段就会自动根据创建单据的时间和修改单据的时间被赋值。很好用，就不用自己手动去更新了

##2、utf8mb4字符集

mysql中保存4字节长度的UTF-8字符(例如emoji表情)，就需要使用utf8mb4字符集。

MySQL的utf8字符集只支持最多三个字节的字符。真正的UTF-8可能会出现四个字节的字符。所以要使用utf8mb4字符集。utf8mb4兼容utf8，且比utf8能表示更多的字符。

只有mysql5.5.3版本以后的才支持utf8mb4字符集，可使用以下语句查看：

    查看版本：select version();    
	查看字符集设置语句是： SHOW VARIABLES LIKE 'character_set%';    
	查看字符集排序方式 ： SHOW VARIABLES LIKE 'collation%'; 

	修改mysql编码格式    
	在安装目录下的my.ini文件中进行配置修改     
	[client]     
	default-character-set = utf8mb4        
	[mysql]     
	default-character-set = utf8mb4        
	[mysqld]     
	character-set-client-handshake = FALSE     
	character-set-server = utf8mb4     
	collation-server = utf8mb4_unicode_ci     
	init_connect=’SET NAMES utf8mb4’ 

修改完成后保存，然后重启mysql服务即可。以后建议都使用utf8mb4字符集，虽然会多占一些空间，不过没关系。