---
layout: post
title:  "Linux命令操作HDFS文件系统"
date:   2018-11-26
desc: "Linux命令操作HDFS文件系统"
keywords: "BigData,HDFS"
categories: [big]
tags: [BigData, HDFS]
---

# Linux命令操作HDFS文件系统

HDFS命令操作

	# 格式化操作
	$ bin/hdfs namenode -format
	# 展示文件和文件夹列表
	$ bin/hdfs dfs -ls /
	# 创建文件夹
		# 在用户目录下创建
		$ bin/hdfs dfs -mkdir AAA/
		# 在根目录下创建
		$ bin/hdfs dfs -mkdir /AAA/
		# 递归层级创建多个文件夹
		$ bin/hdfs dfs -mkdir -p /BBB/CCC
		# 上传本地文件到HDFS，如果上传成功，则删除本地文件（就是剪切操作）
		$ bin/hdfs dfs -moveFromLocal hello_world.txt /
		# 末尾追加数据到HDFS中已经存在的文件里面
		$ bin/hdfs dfs -appendToFile hello_world2.txt /hello_world.txt
		# 查看文件内容
		$ bin/hdfs dfs -cat /hello_world.txt 
		# 查看文件末尾信息
		$ bin/hdfs dfs -tail /hello_world.txt
		# 拷贝本地文件到HDFS，-copyFromLocal可以替换为-put
		$ bin/hdfs dfs -copyFromLocal words.txt /AAA
		# 拷贝HDFS文件到HDFS另一个目录
		$ bin/hdfs dfs -cp /AAA/words.txt /BBB
		# 或剪切
		$ bin/hdfs dfs -mv /AAA/words.txt /BBB
		# 下载HDFS中的文件到本地文件系统
		$ bin/hdfs -get /hello_world.txt ./
		# 下载多个文件，并合并为一个文件到本地文件系统
		$ bin/hdfs dfs -getmerge /*.txt ./demo.txt
		# 删除文件或文件夹
		$ bin/hdfs dfs -rmr /AAA
