---
title: MacOs应用程序图标显示异常
category: MacOs
tag: MacOs
date: 2020-02-20 19:34:20
---

##### 问题：图标显示异常如下图：
![图标显示异常.png](/images/mac-os/icon-error.png)

##### 解决方法：
1. 打开终端，输入：
`sudo find /private/var/folders/ \( -name com.apple.dock.iconcache -or -name com.apple.iconservices \) -exec rm -rfv {} \;`
2. 输入密码：
![icon-error-password.png](/images/mac-os/icon-error-password.png)
3. 输入：
` sudo rm -rf /Library/Caches/com.apple.iconservices.store;`
4. 输入：
`killall Dock`
6. 输入：
`killall Finder`
7. 查看应用程序，已恢复
![icon-error-success.png.png](/images/mac-os/icon-error-success.png)