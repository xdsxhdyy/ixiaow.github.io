---
layout: post
title:  "extjs锁定grid行，点击不可选中"
date:   2018-07-29
desc: "extjs锁定grid行，点击不可选中"
keywords: "java web,extjs"
categories: [javaweb]
tags: [java web, extjs]
---


# extjs锁定grid行，点击不可选中


this.grid.getSelectionModel().lock();**锁住grid不可被选中**

this.grid.getSelectionModel().unlock();**解锁grid可以选中**

可以用在权限的管理里面，没有权限grid就不能选中，通过按钮操作赋予权限后才可编辑选中。

### little note：

这两天系统升级win10遇到的问题，巨坑。win10系统里Tomcat最好不要装在C盘，因为C盘权限的问题，项目会发布不进去，就是项目不会加载进Tomcat\apache-tomcat-6.0.36\webapps里面，装其他盘就没问题了。
