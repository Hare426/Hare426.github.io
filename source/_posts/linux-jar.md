---
title: Linux jar包运行与关闭
category: Linux
tag: Linux
date: 2020-01-09 11:32:20
---

#### Linux jar包运行与关闭
##### 查看进程命令
```
ps aux|grep ventilator
ps -ef|grep 8001
```

> + ps命令将某个进程显示出来
> + grep命令是查找
> + | 是管道命令 是指ps命令与grep同时执行
> + ventilator 为jar包相关信息

![查看进程.png](/images/linux/查看进程.png)

##### 结束进程命令
```
kill -9 1753
```

> + 1753 为 PID
> 


##### startup.sh 文件编写
```
log_name="$(date "+%Y%m%d").log"
echo ${log_name}
nohup java -Xms1024m -Xmx3072m -Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=8002 -jar ventilator-dev.jar > ${log_name} & echo $! > ventilator-dev.pid
tail -f ${log_name}
```

> + ventilator-dev 为jar包名
> + pid 会记录到 ventilator-dev.pid 文件内
>

##### shutdown.sh 文件编写

```
kill -9 `cat ventilator-dev.pid`
```
> + 杀掉 ventilator-dev.pid 文件内记录的 进程
> 

##### ventilator-dev.pid 文件
```
1753
```
> + 进程pid
> + ventilator-dev 为 jar包名
> 


