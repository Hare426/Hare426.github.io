---
title: mysql常见问题处理
category: Back-end
tag: Mysql
date: 2019-11-27 21:05:20
---
#### 1. 终端登录mysql
```
mysql -u root -p
```
其中，***root*** 为用户名，Enter后输入密码；

#### 2. The server time zone value 'ÖÐ¹ú±ê×¼Ê±¼ä' is unrecognized or represents more than one time zone.
```
show variables like '%time_zone%';
```
```
set global time_zone='+8:00';
```
